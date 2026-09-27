# Azure Resource Manager Connector Setup (Publish Code to Azure)

The Azure Resource Manager (ARM) connector provisions Azure App Service hosting for generated applications and **publishes their code**: App Service plans, Linux web apps, app settings, startup commands, managed identities, role assignments, and zip deployments through Kudu using Entra authentication (no publishing passwords). It runs as its own micro-service, `<api-app>-arm`. Generated repositories additionally release through their own pipeline identity (GitHub Actions, Azure Pipelines, or Bitbucket Pipelines).

> **Swagger / API docs:** `https://<api-app>-arm.azurewebsites.net/docs` (your environment) · [reference environment](https://agentic-sdlc-api-my-arm.azurewebsites.net/docs) · [committed OpenAPI contract](openapi/azure-arm.openapi.json). The Swagger page is anonymous; every `/api/v1` call and `/health/ready`, `/health/connectivity` need the `X-Connector-Api-Key` header — select **Authorize** in Swagger UI and paste the key (see [Service API Key Handling](README.md#service-api-key-handling)).

## Table of Contents

- [Identities Involved](#identities-involved)
- [What You Will Configure](#what-you-will-configure)
- [Step 1. Choose the Target Resource Group](#step-1-choose-the-target-resource-group)
- [Step 2. Grant the Connector Identity](#step-2-grant-the-connector-identity)
- [Step 3. Configure the Factory](#step-3-configure-the-factory)
- [Step 4. Deploy and Test the ARM Connector Service](#step-4-deploy-and-test-the-arm-connector-service)
- [GitHub Actions Release Identity](#github-actions-release-identity)
- [Azure Pipelines Release Identity](#azure-pipelines-release-identity)
- [Bitbucket Pipelines Release Identity](#bitbucket-pipelines-release-identity)
- [API Reference (Swagger)](#api-reference-swagger)
- [Troubleshooting](#troubleshooting)

## Identities Involved

| Identity | Purpose | Credential |
| --- | --- | --- |
| System-assigned managed identity of `<api-app>` and `<api-app>-arm` | Create App Service plans/web apps, set settings, publish zip packages | None |
| Generated-app OIDC identity (`GITHUB_OIDC_CLIENT_ID`) | Generated GitHub repositories deploy with `azure/login` federation | Federated credential, no secret |
| ARM workload identity service connection (`ADO_AZURE_SERVICE_CONNECTION`) | Generated Azure Pipelines deploy | Workload identity federation |
| Bitbucket OIDC identity (`BITBUCKET_AZURE_CLIENT_ID`) | Generated Bitbucket Pipelines deploy | Federated credential |

Do **not** set `AZURE_CLIENT_ID`, `AZURE_TENANT_ID` or `AZURE_CLIENT_SECRET` on the factory API or `<api-app>-arm`; `AZURE_CLIENT_ID` would redirect `DefaultAzureCredential` to another identity. There is no `AZURE_LIVE` switch: `azureProvisioning.useMock: false` enables live calls.

## What You Will Configure

| Setting | Where | Value |
| --- | --- | --- |
| `azureProvisioning.enabled` / `useMock` | [integrations.config.json](https://github.com/csdmichael/Foundry-Agentic-Workflow-SDLC/blob/main/api/src/config/integrations.config.json) | `true` / `false` |
| `azureProvisioning.subscriptionId`, `resourceGroup`, `location` | Same | `<generated-app-subscription-id>`, `<generated-app-resource-group>`, `<region>` |
| `azureProvisioning.sku` | Same | Preferred App Service SKU for generated apps (default `F1`) |
| `azureProvisioning.skuFallbackOrder` / `skuFallbackEnabled` | Same | SKUs tried in order when the preferred one hits a capacity or quota limit, lowest list price first: `F1`, `B1`, `B2`, `B3`, `P0v3`, `S1`. Non-capacity errors such as 403 are not retried. |

## Step 1. Choose the Target Resource Group

Create (or select) a resource group dedicated to generated applications, separate from the factory's own resources where policy allows:

```powershell
az group create --name <generated-app-resource-group> --location <region> --subscription <generated-app-subscription-id>
```

**Verify step 1**: `az group show -n <generated-app-resource-group> --query properties.provisioningState -o tsv` → `Succeeded`.

## Step 2. Grant the Connector Identity

Grant **Website Contributor** and **Web Plan Contributor** on the target resource group to both managed identities. The provisioning script does this for `<api-app>-arm` with `-GrantArmRoles -TargetResourceGroup`:

```powershell
./scripts/connector-services/New-ConnectorServiceApps.ps1 -ResourceGroup <factory-resource-group> -ApiAppName <api-app> `
    -Services azure-arm -GrantArmRoles -TargetResourceGroup <generated-app-resource-group>
$api = az webapp identity show -g <factory-resource-group> -n <api-app> --query principalId -o tsv
$scope = az group show -n <generated-app-resource-group> --query id -o tsv
foreach ($role in 'Website Contributor', 'Web Plan Contributor') {
    az role assignment create --assignee-object-id $api --assignee-principal-type ServicePrincipal --role $role --scope $scope --output none
}
```

Portal path: `https://portal.azure.com/#@<tenant>/resource/subscriptions/<generated-app-subscription-id>/resourceGroups/<generated-app-resource-group>/users` → **Add → Add role assignment** → role → **Managed identity** → *App Service* → `<api-app>-arm`.

Grant **Role Based Access Control Administrator** (constrained to those two roles) only if the factory itself must create role assignments. Never grant Owner to clear a 403; inspect the missing action instead.

**Verify step 2**

```powershell
$arm = az webapp identity show -g <factory-resource-group> -n <api-app>-arm --query principalId -o tsv
az role assignment list --assignee $arm --scope $scope --query "[].roleDefinitionName" -o tsv
```

Expected: `Website Contributor` and `Web Plan Contributor`.

## Step 3. Configure the Factory

Set `azureProvisioning` values (Step "What You Will Configure") in your repository copy, commit, and deploy the factory API and the ARM connector service.

## Step 4. Deploy and Test the ARM Connector Service

Deploy with **GitHub > Actions > Deploy connector service - Azure Resource Manager**, then:

```powershell
./scripts/connector-services/test-azure-arm-connector.ps1 -ResourceGroup <factory-resource-group> -ApiAppName <api-app>
./scripts/connector-services/test-azure-arm-connector.ps1 -ResourceGroup <factory-resource-group> -ApiAppName <api-app> `
    -Create -PlanName <existing-plan-in-target-rg> -Cleanup
```

The write test creates a disposable Python web app on an existing plan, sets its startup command, **publishes a zip package through Kudu with the managed identity's Entra token**, waits for `status=success`, requests the site until it serves the published page, and deletes the web app.

Expected (reference environment):

```text
[PASS] 4. Readiness /health/ready     HTTP 200 status=ready
         ok       azureProvisioning.subscriptionId         configured
         ok       azureProvisioning.resourceGroup          configured
         ok       AZURE_CLIENT_SECRET not set              managed identity is used
[PASS] 5. Connectivity (live read)    282 ms {"resourceGroup":"<generated-app-resource-group>","provisioningState":"Succeeded","caller":{"identityType":"app", ...}}
[PASS] 6b. Create web app             https://sdlc-verify-<stamp>.azurewebsites.net created=True
[PASS] 6c. Set startup command        HTTP 200
[PASS] 6d. Publish zip package        accepted=True bytes=196
[PASS] 6e. Deployment completed       status=success complete=True
[PASS] 6f. Published site responds    https://sdlc-verify-<stamp>.azurewebsites.net HTTP 200
[PASS] 6g. Delete disposable web app  deleted=True
```

## GitHub Actions Release Identity

| Item | Setup | Verify |
| --- | --- | --- |
| App registration | Separate, narrowly scoped app `agentic-sdlc-generated-apps`; grant it **Website Contributor** on the target resource group | `az role assignment list --assignee <client-id>` |
| Factory settings | `GITHUB_OIDC_CLIENT_ID=<client-id>`, `GITHUB_OIDC_TENANT_ID=<tenant-id>` on `<api-app>` | Release stage shows the identity |
| Federated credentials | Issuer `https://token.actions.githubusercontent.com`, audience `api://AzureADTokenExchange`, **exact** subject `repo:<owner>@<owner-id>/<repo>@<repo-id>:environment:production` (ID-qualified) | `az ad app federated-credential list --id <client-id>` |
| Automatic creation | Make the factory API identity an **owner** of that app and grant Graph `Application.ReadWrite.OwnedBy` (application) with admin consent; or a platform admin pre-creates the credential and you set `GITHUB_FIC_PREPROVISIONED=1` after verifying it | A generated repo's release job logs in without a secret |

## Azure Pipelines Release Identity

Pre-create an **Azure Resource Manager → Workload identity federation** service connection scoped to the target resource group, authorize it for the generated pipeline only, and set `ADO_AZURE_SERVICE_CONNECTION=<name>`. See [Azure DevOps](azure-devops.md#azure-pipelines-service-connection).

## Bitbucket Pipelines Release Identity

Set `BITBUCKET_AZURE_CLIENT_ID` / `BITBUCKET_AZURE_TENANT_ID` and exact `azureOidc` issuer, audience, subject, repository UUID and deployment-environment UUID from trusted Bitbucket metadata. The current code requires audience `api://AzureADTokenExchange`; a token with Bitbucket's workspace audience fails. Never invent claims or mark externally managed trust before verification.

## API Reference (Swagger)

| Item | Location |
| --- | --- |
| Swagger UI (your environment) | `https://<api-app>-arm.azurewebsites.net/docs` |
| ReDoc (your environment) | `https://<api-app>-arm.azurewebsites.net/redoc` |
| OpenAPI JSON (your environment) | `https://<api-app>-arm.azurewebsites.net/openapi.json` |
| Swagger UI (reference environment) | [https://agentic-sdlc-api-my-arm.azurewebsites.net/docs](https://agentic-sdlc-api-my-arm.azurewebsites.net/docs) |
| OpenAPI JSON (reference environment) | [https://agentic-sdlc-api-my-arm.azurewebsites.net/openapi.json](https://agentic-sdlc-api-my-arm.azurewebsites.net/openapi.json) |
| Committed contract | [openapi/azure-arm.openapi.json](openapi/azure-arm.openapi.json) ([interactive viewer](https://petstore.swagger.io/?url=https://raw.githubusercontent.com/csdmichael/Foundry-Agentic-Workflow-SDLC-Docs/main/docs/setup/connectors/openapi/azure-arm.openapi.json)) |

| Method | Path | Purpose |
| --- | --- | --- |
| GET | `/health`, `/health/ready`, `/health/connectivity` | Probes (connectivity reads the target resource group and reports the caller identity) |
| GET | `/api/v1/resource-group` | Target resource group |
| POST | `/api/v1/plans` | Create or reuse a Linux plan |
| POST | `/api/v1/webapps` | Create or reuse a Linux web app |
| GET | `/api/v1/webapps/{name}` | Web app summary (runtime, SCM host) |
| PUT | `/api/v1/webapps/{name}/appsettings`, `/startup-command` | Configure |
| POST | `/api/v1/webapps/{name}/managed-identity` | Enable system identity |
| POST | `/api/v1/role-assignments?principalId=&role=` | Website/Web Plan Contributor only |
| POST | `/api/v1/webapps/{name}/deployments/zip` | **Publish code** (multipart `package`, max 200 MB) |
| GET | `/api/v1/webapps/{name}/deployments/latest` | Deployment status |
| POST | `/api/v1/webapps/{name}/restart` | Restart |
| DELETE | `/api/v1/resources?resourceId=&confirm=DELETE` | Delete a disposable site or plan in the target RG |

## Troubleshooting

| Symptom | Cause | Fix |
| --- | --- | --- |
| Connectivity 502 `(403) AuthorizationFailed` | Roles missing on the target resource group | Step 2. |
| Connectivity 503 `No Azure credential` | Managed identity disabled | `az webapp identity assign -g <factory-resource-group> -n <api-app>-arm`. |
| 6d 502 `(401)` from Kudu | Identity lacks Website Contributor on the site | Step 2; wait 5 minutes for RBAC propagation. |
| 6e `status=failed` | Package/runtime mismatch | Check `https://<site>.scm.azurewebsites.net/api/deployments/latest/log`. |
| `caller.appId` is not the web app | `AZURE_CLIENT_ID` set | Remove it. |
| Release fails with `reached the limit of 10 Free Linux app service plan(s)` and `SKUs tried: ...` | Every SKU in `skuFallbackOrder` hit a limit | Delete unused plans in the region or add a paid SKU to `skuFallbackOrder`, then **Retry automation**. |
