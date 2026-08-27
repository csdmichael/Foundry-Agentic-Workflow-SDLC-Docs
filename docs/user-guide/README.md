# Agentic SDLC User Guide

<img src="../Agents/12-Agents.png" alt="All 12 ordered Agentic SDLC Prompt Agents from 010 through 120" width="100%">

The Agentic SDLC Software Factory uses 12 ordered Microsoft Foundry Prompt Agents (`010` through `120`), Microsoft Agent Framework orchestration, Azure API Management, and nine human approval gates. This guide follows one real project, **Retail Store Task Execution**, from intake to a published Angular/Ionic application and FastAPI service, then covers parallel project sets and the updated phone, iPad, and web workspaces.

Agent output is always a proposal first. Saving a draft does not write to Azure DevOps, GitHub, or Azure. External publication occurs only when an authorized human approves the matching gate.

## Live Retail sample

| Component | URL |
| --- | --- |
| Agentic SDLC project | <https://agentic-sdlc-ui-my.azurewebsites.net/projects/f2cbe11d-1812-40e4-85c5-085f78daf72d> |
| Azure DevOps project | <https://dev.azure.com/csdmichael/Retail%20Store%20Task%20Execution> |
| Azure DevOps backlog | <https://dev.azure.com/csdmichael/Retail%20Store%20Task%20Execution/_backlogs/backlog> |
| Azure DevOps test plan 426 | <https://dev.azure.com/csdmichael/Retail%20Store%20Task%20Execution/_testPlans/define?planId=426> |
| Azure DevOps Project Overview wiki | <https://dev.azure.com/csdmichael/05a6eec7-fc29-46c3-9b6f-2cdfa37fe6df/_wiki/wikis/01cb9f36-8e63-426e-b57d-91b623d3fc73?pagePath=%2FProject-Overview> |
| GitHub repository | <https://github.com/csdmichael/retail-store-task-execution> |
| Pull request 1 | <https://github.com/csdmichael/retail-store-task-execution/pull/1> |
| Successful feature-branch CI | <https://github.com/csdmichael/retail-store-task-execution/actions/runs/32870761400> |
| Successful Azure deployment | <https://github.com/csdmichael/retail-store-task-execution/actions/runs/32873630512> |
| Published UI | <https://retail-store-task-execution-ui.azurewebsites.net> |
| Published API health | <https://retail-store-task-execution-api.azurewebsites.net/health> |
| Swagger UI | <https://retail-store-task-execution-api.azurewebsites.net/docs> |
| OpenAPI document | <https://retail-store-task-execution-api.azurewebsites.net/openapi.json> |

## Sample inputs

The walkthrough uses all three source documents in `docs/samples/Retail Store Task Execution`:

| Document | Purpose |
| --- | --- |
| `Retail Store Task Execution - Requirements.docx` | Business scope, roles, workflows, acceptance criteria, and non-functional requirements |
| `Retail Store Task Execution - Technical Requirements.docx` | Angular/Ionic UI, FastAPI services, data, identity, integration, and Azure constraints |
| `Retail Store Task Execution - UX Mockups.docx` | Screen inventory, interaction behavior, responsive expectations, and accessibility guidance |

Each file is committed to the generated GitHub repository under `docs/intake/`. Text is extracted separately and supplied to agents as bounded, labelled context so all three sources contribute without exhausting the model budget.

## Roles and accountability

| Human role | Primary responsibility |
| --- | --- |
| Requester | Request access with identity and business justification |
| Business User or Product Owner | Submit scope and approve plan, backlog, and improvement outcomes |
| IT User, Architect, or Developer | Review architecture, generated code, test evidence, and release readiness |
| QA or Security Reviewer | Review automated evidence, controls, mitigations, and residual risk |
| App Owner | Manage users, permissions, connectors, agents, settings, audit, and authorized gate recovery |
| Guest | Read approved artifacts without write access |

The FastAPI service enforces permissions and gate prerequisites. Disabled buttons are useful guidance, but they are not the security boundary.

## The governed workflow

| # | Agent | Proposal or evidence | Human checkpoint |
| --- | --- | --- | --- |
| 010 | Requirements Agent | Scope, actors, requirements, constraints | Backlog Generation Approval |
| 020 | Planning Agent | Epic, Features, User Stories, Tasks, estimates | Backlog Generation Approval |
| 030 | Architecture Advisor Agent | Architecture, dataflow, contracts, security design | Architecture and Design Approval |
| 040 | Code Generation Agent | Application scaffold, tests, CI/CD, pull request | Code Generation Approval |
| 050 | Test Planning Agent | Test strategy, plan, suites, and cases | Test Acceptance Approval |
| 060 | Testing Agent | Execution assessment and quality evidence | Test Acceptance Approval |
| 070 | Test Automation Agent | Automated-run evidence and test-plan publication | Test Acceptance Approval |
| 080 | Security and Compliance Agent | Prioritized controls, mitigations, residual risk | Security Review Approval |
| 090 | DevOps / Release Agent | Hosting, OIDC, merge, deployment, release record | Release and Deployment Approval |
| 100 | Ops Monitoring Agent | Health indicators, alerts, dashboards, triage | Operate and Improve Approval |
| 110 | Knowledge Assistant | Advisory operational and onboarding brief | Advisory after release |
| 120 | Insights Agent | Metrics, thresholds, risks, improvement backlog | Operate and Improve Approval |

All model calls travel through the configured APIM gateway to the named Foundry agents. Microsoft Agent Framework owns workflow topology, prerequisite checks, approval pause/resume, and checkpoints.

## Parallel project sets and device layouts

Use **New Project Set** when several independent applications should enter the
SDLC together. The wizard accepts 2 to 20 projects. Each project requires a
functional requirements document; UX mockups and technical requirements are
optional. Every project defaults to **Autonomous**, while Minimal review and
Human review can be selected independently.

On a phone, project entries are stacked and the fixed bottom workspace keeps
Dashboard, Existing Projects, Approvals, Workflow Runs, and the full menu within
thumb reach.

<img src="images/04a-project-set-intake-phone.png" alt="Phone layout for manual multi-project intake" width="100%">

Agent participation and model deployments are selected once for the set. Use a
project's **Override** button to clone and replace the shared configuration for
that project only. Other projects continue to inherit the set policy.

<img src="images/08c-project-set-model-configuration.png" alt="Web layout for shared and per-project model configuration" width="100%">

ZIP import is the faster alternative. Put one project in each top-level folder.
Include one functional requirements file and optionally one UX mockup and one
technical requirements file. An optional `project.json` can provide the project
name, owners, environment, execution mode, and explicit document paths. The API
validates the complete archive before provisioning starts.

The confirmation step shows every project, its execution mode, shared-agent
policy, and the fact that all workflow runs launch independently in parallel.

<img src="images/08d-project-set-confirm-mixed-modes.png" alt="Four-project confirmation with Autonomous, Minimal review, and Human review modes" width="100%" data-frame="full-window">

After confirmation, the API creates a distinct workflow run and checkpoint set
for every project and advances eligible runs concurrently. Existing Projects
opens automatically, groups projects by workflow-aware status, and shows stage
progress, environment, owner, project-set membership, and drill-through.

<img src="images/10a-project-status-progress-ipad.png" alt="iPad layout with status-grouped projects and workflow progress KPIs" width="100%">

Stage progress updates while Foundry agents are active. Running cards use an
animated progress sheen and status pulse; the caption identifies the current
stage and how many agents in that stage have completed.

<img src="images/10b-live-project-progress-mixed-modes.png" alt="Existing Projects with live lifecycle percentage and current-stage progress" width="100%" data-frame="full-window">

Each card also exposes a **Trace** control. Its collapsed state shows the latest
message and current stage. Expanding it reveals the chronological project trace,
including ADO/GitHub provisioning, generated work items, code and pull-request
evidence, state transitions, pipeline outcomes, Azure publication, wiki updates,
and workflow completion, with direct links to external evidence.

<img src="images/10c-expanded-project-trace.png" alt="Expanded project trace with generated artifacts, approvals, ADO states, and evidence links" width="100%" data-frame="full-window">

Project Details uses the same live state. The active agent receives a restrained
activity ring while queued and completed agents remain visually stable.

<img src="images/13c-running-agent-animation.png" alt="Running Architecture Advisor Agent highlighted in the project workflow" width="100%" data-frame="full-window">

The iPad layout uses a persistent icon rail and two-column work area. Web uses
the full grouped sidebar and information-dense panels. All three layouts expose
the same server-authorized actions and records.

## End-to-end walkthrough

### 1. Request access

Use Microsoft Entra ID, email OTP, or read-only guest access. A new requester can submit a work email and business reason for App Owner review.

<img src="images/01-requester-sign-in.png" alt="Agentic SDLC sign-in options" width="100%">

<img src="images/02-requester-request-access.png" alt="Request access form" width="100%">

### 2. Assign a least-privileged role

The App Owner creates or updates the user, selects the authentication provider, and assigns the role that matches the person's responsibilities.

<img src="images/03-app-owner-user-management.png" alt="App Owner user management" width="100%">

### 3. Define the Retail project

Enter the project outcome, accountable Business and IT owners, and target environment. The Retail sample focuses on prioritized store tasks, photographic proof, offline capture, exception handling, and same-day district compliance visibility.

<img src="images/04-business-owner-project-intake.png" alt="Retail project name, description, owners, and environment" width="100%">

### 4. Upload all three intake documents

Requirements are required. Technical requirements and UX mockups are optional but strongly recommended. Each upload is limited to 25 MB. Do not place passwords, tokens, keys, or connection strings in documents or prompts.

<img src="images/05-business-owner-requirements-and-mockups.png" alt="Retail requirements, technical requirements, and UX mockup uploads" width="100%">

### 5. Confirm Azure DevOps and GitHub targets

The wizard derives a private ADO project and a GitHub repository from the project name. Visibility and target names remain visible before submission.

<img src="images/06-business-owner-devops-targets.png" alt="Retail Azure DevOps and GitHub targets" width="100%">

### 6. Review systems of record

Confirm the effective work-item, test-management, source-control, build, and deployment systems. Project overrides remain explicit and auditable.

<img src="images/07-business-owner-systems-of-record.png" alt="Retail systems of record" width="100%">

### 7. Select the complete agent workflow

All 12 agents are selected for this run. Future agents remain disabled until every configured prerequisite gate is approved.

<img src="images/08-retail-agent-selection.png" alt="All 12 Retail workflow agents selected" width="100%">

### 8. Confirm and submit

The final wizard step summarizes ownership, environment, all three files, target systems, and selected agents. Submission provisions the project systems and creates the workflow run.

<img src="images/09-retail-confirm-submit.png" alt="Retail project confirmation" width="100%">

### 9. Verify provisioned systems

The project record links the generated ADO project and GitHub repository. A failed connector must be repaired before an agent can publish to it.

<img src="images/10-retail-provisioned-systems.png" alt="Provisioned Retail ADO project and GitHub repository" width="100%">

### 10. Review the project overview

The project page displays owners, environment, current stage, systems of record, lifecycle, approval gates, and the 12-agent lane in one operating view.

<img src="images/11-retail-project-overview.png" alt="Retail project overview" width="100%">

### 11. Approve Plan and Scope

The first gate confirms that the submitted business outcome and source material are suitable for agent work. Approval unlocks Requirements and Planning; it does not publish backlog items by itself.

<img src="images/12-retail-plan-scope-approval.png" alt="Retail Plan and Scope approval" width="100%">

### 12. Review Requirements and Planning proposals

Long proposals begin in a compact three-row summary. Reviewers can show the full proposal, edit it, save a draft, and select exactly which artifacts are eligible for approval.

<img src="images/13-retail-planning-review.png" alt="Retail Planning Agent proposal review" width="100%">

### 13. Review the ADO hierarchy

Planning output appears as a collapsed Epic to Feature to User Story to Task tree. Titles, types, indentation, and child counts remain visible while detail editors stay closed. Selecting a child work item does not automatically select its parents. Clearing a parent clears its branch. Work-item deletion preserves the current review position and keyboard focus.

The approved Retail hierarchy created:

- 1 Epic
- 3 Features
- 6 User Stories
- 18 Tasks
- 3 dated sprints
- 6 shared queries
- 2 dashboards
- 1 delivery plan

<img src="images/14-retail-work-item-hierarchy.png" alt="Collapsed Retail ADO work-item hierarchy" width="100%">

Backlog Generation Approval requires the Planning Agent artifact. A Requirements summary alone cannot satisfy the gate.

### 14. Review generated code through a pull request

Code Generation produced branch `feature/agent/e724c65d` and pull request 1 with the UI, API, database assets, tests, CI, and Azure deployment workflow. Pull Request Review cannot be approved until a real pull-request URL is present.

<img src="images/15-retail-github-pull-request.png" alt="Retail generated-code pull request" width="100%">

### 15. Accept test evidence

Test Planning created ADO Test Plan 426. Testing and Test Automation referenced successful GitHub Actions CI run 32870761400 for the exact feature-branch SHA. The same ADO plan was reused during the approval retry rather than duplicated.

<img src="images/16-retail-test-acceptance-review.png" alt="Retail Test Acceptance review" width="100%">

### 16. Approve security and compliance

The Security and Compliance Agent provides an executive release assessment with prioritized findings, control evidence, mitigations, residual risk, and a release recommendation. Security output remains proposal-only until this gate approves it.

<img src="images/17-retail-security-review.png" alt="Retail Security Review proposal" width="100%">

### 17. Approve release and deployment

Before approval, PR 1 was open, clean, mergeable, and backed by successful CI. The DevOps / Release Agent proposed Azure resources, OIDC configuration, smoke checks, rollback, and go/no-go criteria.

<img src="images/18-retail-release-approval.png" alt="Retail Release and Deployment proposal" width="100%">

Release approval then:

1. provisioned the generated API and UI App Services idempotently;
2. configured repository variables and secret-free GitHub OIDC login;
3. merged PR 1 exactly once at commit `5a576ac4c6d28382836642813e8ca93d23e3c546`;
4. let the merge push trigger one `deploy-azure.yml` run;
5. verified the published UI, `/health`, Swagger, OpenAPI, seed data, and CRUD flow; and
6. created the ADO `/Project-Overview` wiki page with hosted links and release context.

Fresh merges are not followed by a second manual dispatch. Same-ref workflow concurrency prevents competing App Service deployments.

### 18. Review operations and improvement proposals

After release, Ops Monitoring defines service indicators, alerts, dashboard views, incident triage, ownership, rollback signals, and a first-week checklist. Knowledge Assistant produces the support brief. Insights defines measurable adoption and reliability thresholds plus an ordered 30-day improvement backlog.

<img src="images/19-retail-operate-improve-review.png" alt="Retail Operate and Improve review" width="100%">

### 19. Confirm the completed project

All lifecycle stages and all nine approval decisions are complete. The project record remains the starting point for evidence and operations.

<img src="images/20-retail-completed-project.png" alt="Completed Retail project" width="100%">

The final five agents retain their Foundry identity, completion state, last run, checkpoint, and prerequisite context.

<img src="images/21-retail-completed-agents.png" alt="Completed Security, Release, Operations, Knowledge, and Insights agents" width="100%">

### 20. Verify all nine gates

The durable gate table records approver, role, selected artifacts, decision, and status. A closed gate cannot silently rerun its publication action. App Owners can reopen a gate only with an audit comment.

<img src="images/22-retail-approved-gates.png" alt="All nine Retail approval gates approved" width="100%">

## Published result

### Retail operator application

The deployed Angular 18 and Ionic 8 UI loads the generated Retail execution records from the published FastAPI service.

<img src="images/25-retail-published-application.png" alt="Published Retail Store Task Execution application" width="100%">

### FastAPI and OpenAPI contract

Swagger exposes `/health` plus list, create, get, update, and delete operations for `/api/executions`.

<img src="images/26-retail-swagger-api.png" alt="Published Retail FastAPI Swagger UI" width="100%">

### Azure DevOps project wiki

The DevOps / Release Agent now ensures one project wiki and upserts `/Project-Overview` after publication. The page includes:

- project name and description;
- hosted UI, API, health, Swagger, and OpenAPI links;
- GitHub repository, released pull request, deployment workflow/run, and merge commit;
- Business and IT ownership;
- selected UI, API, and data stacks; and
- the human-governance statement.

Release retries update the same page with its current ETag instead of creating duplicates. Resources discovered as pre-existing are never treated as workflow-owned deletion targets.

## Operate and audit

### Agent Activity

Agent Activity correlates every run with its lifecycle stage, status, model route, artifacts, and correlation identifier.

<img src="images/23-retail-agent-activity.png" alt="Retail agent activity" width="100%">

### Audit Trail

Authentication, agent execution, artifact edits, decisions, delegations, connector writes, merges, publication, and gate recovery are recorded with actor and correlation context.

<img src="images/24-retail-audit-trail.png" alt="Retail audit trail" width="100%">

## Approval evidence rules

| Gate | Minimum evidence | What approval unlocks or publishes |
| --- | --- | --- |
| Plan and Scope | Submitted project and intake documents | Requirements and Planning runs |
| Backlog Generation | Selected Planning Agent artifact | ADO hierarchy, sprints, queries, dashboards, delivery plan |
| Architecture and Design | Selected Architecture artifact | Versioned `docs/architecture-design.md` and Code Generation |
| Code Generation | Selected Code Generation artifact | Generated branch and pull request |
| Pull Request Review | GitHub repository evidence with `pullRequestUrl` | Security/Release prerequisite |
| Test Acceptance | Test Planning, Testing, and Test Automation artifacts plus successful run evidence | Security review |
| Security Review | Selected Security and Compliance artifact | Release prerequisite |
| Release and Deployment | Selected DevOps / Release artifact, reviewed PR, test, and security evidence | Merge, Azure publish, ADO wiki, post-release agents |
| Operate and Improve | Selected Ops Monitoring and Insights artifacts | Completed operating lifecycle |

## Release identity prerequisite

Generated repositories use GitHub OIDC and do not store an Azure client secret. The configured Entra application must trust the exact ID-qualified repository/environment subject:

```text
repo:<owner>@<ownerId>/<repository>@<repositoryId>:environment:production
```

The API managed identity needs ownership of the OIDC application and the approved Microsoft Graph permission to maintain only credentials for applications it owns. If an authorized Entra owner pre-creates a verified repository credential for recovery, `GITHUB_FIC_PREPROVISIONED=1` may be enabled only for that release and must be removed immediately afterward.

## Expected traceability chain

A completed project leaves:

- three versioned intake documents in GitHub;
- approved requirements and architecture documents;
- a reviewed ADO backlog and planning assets;
- generated application code and tests behind a pull request;
- accepted CI, test-plan, and security evidence;
- one approved merge and one deployment workflow;
- live UI, API health, Swagger, and OpenAPI endpoints;
- an idempotent ADO Project Overview wiki page;
- post-release operations and improvement proposals; and
- nine attributable human decisions in the audit trail.

## Rebuild this guide

The Markdown file is the source of truth. After changing guide content or UI
captures, rebuild and validate the PDF with installed Chrome:

```powershell
api/.venv/Scripts/python.exe scripts/build_user_guide.py
```

The builder resolves local images, creates a print-safe table of contents,
exports `Agentic-SDLC-User-Guide.pdf`, and verifies the result with `pypdf`.
