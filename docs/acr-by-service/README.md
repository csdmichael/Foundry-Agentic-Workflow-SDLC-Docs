# ACR by Service — Monthly Azure Resource Consumption

## Summary

For **100 projects per month**, this worked planning example estimates:

| Scope | Monthly USD |
| --- | ---: |
| Shared Software Factory, including model inference | **1,102.77** |
| Generated applications: 20 active, dedicated Linux B1 plans | **248.20** |
| **Total Azure consumption estimate** | **1,350.97** |
| Budget envelope with 20% contingency | **1,621.16** |
| Allocated Azure cost per completed project, before contingency | **13.51** |

**ACR means Azure resource consumption in this document, not Azure Container
Registry.** No Container Registry is required by the documented deployment.
This estimate covers the factory and the explicitly assumed generated-app
hosting, not labor, licenses, or arbitrary infrastructure generated for customers.
It complements, rather than replaces, the
[model and labor ROI methodology](../../README.md#cost-usage-and-model-governance).

**Estimate prepared: October 9, 2026. Currency: USD. Reference region: West US 2.**
These are illustrative planning inputs, **not verified current Azure prices, a
customer quote, or measured usage**. Live pricing could not be retrieved when
preparing this example. App Service inputs reuse the September 2026 values
documented in the [setup guide](../setup/README.md#1d-app-service-hosting);
all other rates below are explicitly assumed budget inputs. Replace them with
region-, SKU-, model-, and agreement-specific prices before procurement.
Taxes, negotiated discounts, reservations, free grants, credits, and free
allowances are not applied; this is not a prediction of the net invoice.

## Workload and deployment assumptions

The resource inventory follows the [customer setup guide](../setup/README.md)
and [reference architecture](../../README.md#architecture-at-a-glance).
Resource counts are deployment assumptions, not a scan of a live subscription.

| Variable | Baseline | Meaning / effect on consumption |
| --- | ---: | --- |
| `P` | 100 projects/month | Projects completing the full workflow; partial/failed projects still incur usage and must be added to an actual forecast |
| `A` | 14 agents/project | All lifecycle agents selected: 7 router, 4 `gpt-5.4`, 3 `gpt-5.1-codex` |
| `W` | 2 passes/project | Initial execution plus one equivalent change/rework pass; a sizing assumption, not guaranteed workflow behavior |
| `R` | 1.20 | 20% additional equivalent runs for retries/tool loops beyond those two passes |
| `Tin`, `Tout` | 20,000 / 5,000 tokens/run | All prompt/context input and all billed output, including reasoning tokens where billed; no cached-input discount assumed |
| `H` | 730 hours/month | Average budgeting month; use actual calendar hours for billing reconciliation |
| Factory environments | 1 | One production-oriented reference environment, not production + QA + development |
| App Service | 2 Linux B3 plans, 1 instance each | UI on its own plan; API and seven connectors share the other plan (nine web apps, not nine plans) |
| Cosmos DB | 400 RU/s, 1 region | Dedicated provisioned throughput on the shared `state` container; no autoscale, multi-region writes, or free tier assumed |
| APIM | 1 Standard v2 base instance | Assumed USD 700 monthly base allowance; no additional units or billable request overage in this example |
| Telemetry | `5 + 0.02P` GB/month | Factory, gateway, and generated-app logs together; avoid billing the same ingestion twice |
| Cosmos stored data | `5 + 0.05P` GB | Average retained footprint, not just newly written data; assumes cleanup/retention keeps it bounded |
| Email | `1,000 + 20P` messages/month | Background sign-in/access activity plus project notifications; average billable message size 0.05 MB |
| Secret operations | `10,000 + 100P` /month | Background and project-related Key Vault operations; no paid keys/certificates |
| Private connectivity | 1 Cosmos private endpoint, 1 DNS zone | Existing VNet; APIM-to-Foundry network path must be approved separately |
| Private data / Internet egress | `5 + 0.1P` GB each/month | Separate assumed traffic meters; no inter-region transfer |
| `d`, `L`, `h` | 20%, 1 month, 100% | Fraction of projects retaining a generated app, average retention, and fraction of a month each plan remains provisioned |

There are `P × A × W × R = 3,360` equivalent agent runs per month.
This gives **67.2 million input tokens** and **16.8 million output tokens**.
Prompt growth, large generated repositories, long conversations, tool calls,
failed runs, and change requests can substantially increase those quantities.
Human approval waiting time does not itself consume model tokens, but always-on
infrastructure remains billable.

## Monthly estimate by Azure service

`USD/M` means dollars per million tokens or operations as stated. Each service
amount is rounded to cents (half up) before summation.

| Azure service / meter | Monthly resource consumption | Unit-price input | Calculation | Monthly USD |
| --- | --- | --- | --- | ---: |
| App Service — factory UI, API, seven connectors | 2 Linux B3 plan-months, 1,460 plan-hours | USD 48.91/plan-month, setup-guide reference | `2 × 48.91`; apps sharing a plan add no separate plan charge | 97.82 |
| API Management — Foundry gateway | 1 Standard v2 base instance-month | **Assumed** USD 700/month | `1 × 700`; add any SKU-specific request overage or extra units | 700.00 |
| Microsoft Foundry — all three model deployments | 67.2M input + 16.8M output tokens | **Assumed** per-model rates in the next table | `75.60 + 120.00 + 54.00` | 249.60 |
| Cosmos DB for NoSQL — throughput and storage | 400 RU/s × 730 hours + 10 GB retained | **Assumed** USD 0.008/100 RU/s-hour; USD 0.25/GB-month | `(400 / 100) × 730 × 0.008 + (5 + 0.05 × 100) × 0.25` | 25.86 |
| Azure Monitor — Application Insights / Log Analytics | 7 GB ingested | **Assumed** USD 2.76/GB | `(5 + 0.02 × 100) × 2.76`; charged once at the workspace | 19.32 |
| Communication Services / Email Communication Services | 3,000 emails + 150 MB | **Assumed** USD 0.00025/email + USD 0.00012/MB | `(1,000 + 20 × 100) × (0.00025 + 0.05 × 0.00012)` | 0.77 |
| Key Vault — secrets | 20,000 operations | **Assumed** USD 0.03/10,000 operations | `(10,000 + 100 × 100) / 10,000 × 0.03` | 0.06 |
| Private Link — Cosmos private endpoint | 730 endpoint-hours + 15 GB processed | **Assumed** USD 0.01/hour + USD 0.01/GB | `730 × 0.01 + (5 + 0.1 × 100) × 0.01` | 7.45 |
| Azure Private DNS | 1 zone-month + 200,000 queries | **Assumed** USD 0.50/zone-month + USD 0.40/M queries | `0.50 + (100,000 + 1,000 × 100) / 1,000,000 × 0.40` | 0.58 |
| Azure bandwidth — Internet outbound | 15 GB | **Assumed** USD 0.087/GB; no free allowance applied | `(5 + 0.1 × 100) × 0.087` | 1.31 |
| **Shared factory subtotal** | | | **Sum of the ten rows above** | **1,102.77** |
| App Service — retained generated applications | 20 dedicated Linux B1 plan-months | USD 12.41/plan-month, setup-guide reference | `P × d × L × h × 12.41 = 100 × 0.20 × 1 × 1 × 12.41` | 248.20 |
| **Total** | | | **Factory + generated hosting** | **1,350.97** |

**Model-inference breakdown:** these rates are illustrative inputs, not published
prices for the named deployments. Router cost is a blended assumption; replace
it with the observed underlying model mix and applicable Azure deployment rates.

| Deployment | Agents per pass | Equivalent runs | Input / output (M tokens) | Assumed input / output USD/M | Monthly USD |
| --- | ---: | ---: | --- | --- | ---: |
| `model-router` (Balanced) | 7 | `100 × 7 × 2 × 1.2 = 1,680` | 33.6 / 8.4 | 1.00 / 5.00 | `33.6 × 1 + 8.4 × 5 = 75.60` |
| `gpt-5.4` | 4 | `100 × 4 × 2 × 1.2 = 960` | 19.2 / 4.8 | 2.50 / 15.00 | `19.2 × 2.5 + 4.8 × 15 = 120.00` |
| `gpt-5.1-codex` | 3 | `100 × 3 × 2 × 1.2 = 720` | 14.4 / 3.6 | 1.25 / 10.00 | `14.4 × 1.25 + 3.6 × 10 = 54.00` |
| **Total** | **14** | **3,360** | **67.2 / 16.8** | | **249.60** |

Prompt Agent definitions and Microsoft Agent Framework are not charged as
fourteen independent compute instances here. The example uses token-based
inference, not provisioned model capacity, hosted-agent compute, fine-tuning,
or paid tools. Any such selected meter must be added separately.

## Formulas and scaling

For each model `m`, let `Am` be its agents per pass, `Im` and `Om` its token
volumes per run, and `ri,m`, `ro,m` its USD/M token rates:

- Runs: `Nm = P × Am × W × R`.
- Uncached inference: `Cm = Nm × (Im × ri,m + Om × ro,m) / 1,000,000`.
- With a cached-input fraction `f` and cached rate `rc,m`:
  `Cm = Nm × (Im × ((1 − f) × ri,m + f × rc,m) + Om × ro,m) / 1,000,000`.
  Apply this only to provider-reported eligible cached tokens, not all context.
- Shared consumption: sum the service-row formulas after updating `P`, resource
  counts, capacity, retained data, and rates.
- Generated hosting: `Capps = P × d × L × h × plansPerApp × planMonthlyRate`
  in a steady-state portfolio. Baseline `plansPerApp = 1`. For an actual month,
  sum each plan's provisioned hours × hourly rate instead, including existing
  apps and temporary deployments.
- Total: `Ctotal = Cfactory + Capps + Coptional`.
- Budget: `Cbudget = Ctotal × 1.20`. Contingency is a budget reserve, not a
  separately billed Azure resource.
- Allocated per-project consumption: `Ctotal / P`; this allocation is not the
  marginal cost of adding one project. With zero completed projects, report
  fixed costs directly rather than dividing by zero.

| Projects/month | Shared factory USD | Active generated apps | Generated hosting USD | Total USD | Budget with 20% USD |
| ---: | ---: | ---: | ---: | ---: | ---: |
| 10 | 870.63 | 2 | 24.82 | 895.45 | 1,074.54 |
| **100** | **1,102.77** | **20** | **248.20** | **1,350.97** | **1,621.16** |
| 500 | 2,134.46 | 100 | 1,241.00 | 3,375.46 | 4,050.55 |

These scenarios hold the two B3 plans, 400 RU/s, and APIM base capacity constant;
they are **arithmetic sensitivity examples, not capacity guarantees**. Load-test
before adopting a volume. The reference API cannot safely be horizontally scaled
without distributed coordination. Cosmos throughput and retained storage may
grow nonlinearly, and APIM extra units/overage may change fixed costs.

Generated plans default to **F1**, with paid SKU fallback configured separately
from factory B3 hosting. The example deliberately budgets B1 for retained demos,
not a production SLA. F1 quota/feature limitations may prevent free hosting.
Replace B1 with the actual approved SKU; stopping a web app does **not** eliminate
its paid App Service plan charge. Long-lived apps accumulate: a three-month
retention at the same project volume gives 60 active plans, not 20. Governed
deletion must remove unused plans and related resources to end their charges.

## Other Azure technologies and conditional costs

All rows below have **zero incremental consumption in the worked total** because
no separately billable quantity is assumed. This is a scope choice, not a claim
that every capability is free. Add each selected resource's actual meter.

| Technology | Baseline treatment | Formula / when to add consumption |
| --- | --- | --- |
| Entra ID, managed identities, RBAC, OIDC federation | No incremental premium licenses assumed | `licensed users × incremental monthly license rate`; conditional-access/PIM requirements may require premium licensing |
| Azure Resource Manager and resource groups | No separate orchestration charge assumed; ARM connector compute already in API plan | Sum deployed resources' meters, not a second ARM compute charge |
| Virtual Network / App Service VNet integration | Existing VNet; no peering or paid network appliances assumed | Add peering GB × rate; NAT Gateway, Firewall, VPN/ExpressRoute, public IPs, and gateway hours/data if required |
| Additional private endpoints / DNS | Only Cosmos endpoint/zone counted above | For private Foundry, Key Vault, or additional environments: `endpoints × H × hourly rate + GB × rate + zones/queries`; preserve required private networking |
| Azure Storage (Blob / Files) | Not a baseline prerequisite | `average GB × storage rate + read/write operations × operation rates + retrieval/egress`; add generated-app or tool storage when selected |
| Azure AI Search and embeddings | Not required by the documented setup | `search units × H × hourly rate + embedding tokens / 1M × token rate`; add semantic/vector/tool charges as applicable |
| Azure AI Content Safety | Not enabled merely by a gateway policy configuration | `billable text/image units × applicable rate` when a configured service/policy is used; avoid duplicating included model safety charges |
| Azure Service Bus | Not a baseline prerequisite | Selected tier's base capacity + billable messaging operations |
| Azure SQL / additional Cosmos databases | Generated-app data tiers only; factory Cosmos is not every application's business database | `database compute hours × rate + retained storage/backup GB × rate + other selected meters` |
| Azure Monitor alerts, retention, exports, availability tests | Only ingestion is in the estimate | Add paid alert/time-series/test units and retained/exported GB; sampling affects ingestion |
| Cosmos backup / disaster recovery | No separately charged backup option or extra region assumed | Add paid backup/restore and extra-region throughput/storage; verify chosen backup policy |
| Key Vault keys/certificates, Azure Policy paid guest configuration, Defender for Cloud | Not selected in the example | Add selected protected resources, key operations, certificates, or server-months × applicable rates |
| Private build runner on Azure | Existing network-connected runner assumed; no new VM budgeted | `VM provisioned hours × compute rate + disks + runner telemetry/network`; private Foundry synchronization needs a reachable runner |
| Azure DevOps / Azure Pipelines | Optional system of record; treated as licensing/build services outside this Azure-resource total | Budget paid users, Basic + Test Plans, parallel jobs, artifacts, and any Azure-hosted runner infrastructure separately |

Microsoft 365/SharePoint, GitHub/Copilot/Actions, Jira, Confluence, Bitbucket,
domain registration, support, labor, and third-party model/tool charges are
outside this total. If Copilot replaces a Foundry code-generation path, remove
that path's Azure inference usage and budget its GitHub charges separately.
Additional development/QA environments need their own capacity and network
costs; do not multiply the shared factory subtotal by the number of projects.

## Price sources and reconciliation

Use the following official sources to replace **every assumed rate**, confirm
regional availability and included allowances, and capture the effective date:

- [Azure Pricing Calculator](https://azure.microsoft.com/en-us/pricing/calculator/)
  and [Azure Retail Prices API](https://learn.microsoft.com/en-us/rest/api/cost-management/retail-prices/azure-retail-prices).
- [App Service](https://azure.microsoft.com/en-us/pricing/details/app-service/linux/),
  [API Management](https://azure.microsoft.com/en-us/pricing/details/api-management/),
  [Cosmos DB](https://azure.microsoft.com/en-us/pricing/details/cosmos-db/autoscale-provisioned/),
  and [Foundry models](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/openai-service/).
- [Azure Monitor](https://azure.microsoft.com/en-us/pricing/details/monitor/),
  [Communication Services](https://azure.microsoft.com/en-us/pricing/details/communication-services/),
  and [Key Vault](https://azure.microsoft.com/en-us/pricing/details/key-vault/).
- [Private Link](https://azure.microsoft.com/en-us/pricing/details/private-link/),
  [DNS](https://azure.microsoft.com/en-us/pricing/details/dns/),
  and [bandwidth](https://azure.microsoft.com/en-us/pricing/details/bandwidth/).

For model rates, use the factory's Model Suggestions & Pricing page and
provider-reported tokens. Select meters matching the deployment SKU, geography,
input/output/cached-input units, and effective date; Global model deployments
do not inherit price or residency guarantees solely from the project region.
Do not substitute batch or provisioned-capacity rates for pay-as-you-go runs.

Reconcile monthly against Azure Cost Management exports, tagging factory
resources separately from generated project resources. Compare completed and
failed runs, per-model tokens, APIM requests (conversation creation and Responses
are separate gateway requests), Cosmos RU utilization, retained data, logs,
emails, and each plan's lifetime. Azure billing exports and provider invoices
remain authoritative; update the assumptions after the first representative
month and whenever model routing, retention, environments, or deployment SKUs
change.
