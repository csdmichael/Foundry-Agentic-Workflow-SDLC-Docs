# Azure Consumption by Environment

## Monthly estimate

| Azure service | Dev (USD) | Test (USD) | Prod (USD) | Total ACR (USD) |
| --- | ---: | ---: | ---: | ---: |
| App Service — factory UI, API, connectors | 97.82 | 97.82 | 97.82 | 293.46 |
| API Management — Foundry gateway | 700.00 | 700.00 | 700.00 | 2,100.00 |
| Microsoft Foundry — model inference | 24.96 | 24.96 | 249.60 | 299.52 |
| Cosmos DB — throughput and storage | 24.74 | 24.74 | 25.86 | 75.34 |
| Azure Monitor — logs | 14.35 | 14.35 | 19.32 | 48.02 |
| Email | 0.31 | 0.31 | 0.77 | 1.39 |
| Key Vault | 0.03 | 0.03 | 0.06 | 0.12 |
| Private Link | 7.36 | 7.36 | 7.45 | 22.17 |
| Private DNS | 0.54 | 0.54 | 0.58 | 1.66 |
| Bandwidth | 0.52 | 0.52 | 1.31 | 2.35 |
| Generated-app hosting | 24.82 | 24.82 | 248.20 | 297.84 |
| **Total monthly Azure consumption** | **895.45** | **895.45** | **1,350.97** | **3,141.87** |
| Budget with 20% contingency | 1,074.54 | 1,074.54 | 1,621.16 | 3,770.24 |

**ACR** means Azure consumption in this estimate, not Azure Container Registry.
The total ACR column is the sum of Dev, Test, and Prod. Contingency is a budget
reserve, not an Azure charge.

## Assumptions

- Dev and Test each complete 10 projects per month; Prod completes 100.
- Each environment has its own full factory footprint: two Linux B3 App Service
  plans, one Standard v2 API Management instance, and 400 Cosmos DB RU/s.
  Dev/Test are not downsized, and shared services are not shared between environments.
- Model usage assumes 14 agents per project, two workflow passes, 20% additional
  runs, and 20,000 input / 5,000 output tokens per run. Generated-app hosting
  assumes 20% of completed projects retain one Linux B1 plan for one month.
- Estimates are in USD/month, use a 730-hour month, and are illustrative planning
  inputs—not measured usage, a verified Azure quote, or a net invoice. Rates are
  assumed unless stated otherwise; taxes, credits, discounts, and free allowances
  are excluded. Replace rates and sizing with subscription-, region-, SKU-, and
  agreement-specific values before budgeting.

## Price references

- [Azure Pricing Calculator](https://azure.microsoft.com/en-us/pricing/calculator/)
  and [Azure Retail Prices API](https://learn.microsoft.com/en-us/rest/api/cost-management/retail-prices/azure-retail-prices)
- [App Service](https://azure.microsoft.com/en-us/pricing/details/app-service/linux/)
  and [API Management](https://azure.microsoft.com/en-us/pricing/details/api-management/)
- [Cosmos DB](https://azure.microsoft.com/en-us/pricing/details/cosmos-db/autoscale-provisioned/)
  and [Foundry models](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/openai-service/)
- [Azure Monitor](https://azure.microsoft.com/en-us/pricing/details/monitor/),
  [Communication Services](https://azure.microsoft.com/en-us/pricing/details/communication-services/),
  and [Key Vault](https://azure.microsoft.com/en-us/pricing/details/key-vault/)
- [Private Link](https://azure.microsoft.com/en-us/pricing/details/private-link/),
  [Private DNS](https://azure.microsoft.com/en-us/pricing/details/dns/),
  and [Bandwidth](https://azure.microsoft.com/en-us/pricing/details/bandwidth/)

Validate current rates and reconcile monthly against Azure Cost Management
exports. Add any optional resources or environment-specific capacity not listed
above; actual Azure billing exports and provider invoices remain authoritative.
