# FinOps Quiz

## Question 1
According to the documentation, what is the primary purpose of the FinOps Open Cost and Usage Specification (FOCUS)?

- [ ] To provide a set of automation runbooks for optimising Azure resources.
- [x] To standardise the schema for cost and usage data from various sources.
- [ ] To define a connection protocol for AI agents to query cost data.
- [ ] To visualise cost recommendations and insights in Power BI dashboards.

**Hint:** Consider how this specification helps when dealing with cost data from multiple different cloud providers.

## Question 2
When configuring an AI agent like GitHub Copilot to query a FinOps hub, which protocol is used to connect to Azure Data Explorer?

- [ ] Azure Resource Graph (ARG)
- [ ] FinOps Open Cost and Usage Specification (FOCUS)
- [x] Model Context Protocol (MCP)
- [ ] Kusto Query Language (KQL)

**Hint:** This protocol is supported by the Azure MCP server and allows various AI clients to connect.

## Question 3
What is the key difference between 'BilledCost' and 'EffectiveCost' in the FOCUS data model?

- [ ] 'BilledCost' includes taxes, while 'EffectiveCost' does not.
- [x] 'BilledCost' reflects charges as they appear on an invoice, while 'EffectiveCost' amortises upfront purchases over their term.
- [ ] 'BilledCost' is denominated in the local currency, while 'EffectiveCost' is always in USD.
- [ ] 'BilledCost' represents list prices, while 'EffectiveCost' includes negotiated discounts.

**Hint:** Think about how one-time purchases, like reservations, are represented for monthly chargeback purposes.

## Question 4
In the context of the Azure Optimization Engine (AOE), what is the recommended way to handle performance issues or secure the environment with private endpoints?

- [ ] Increase the memory limits of the Azure Automation sandbox directly.
- [x] Execute the runbooks from a Hybrid Worker instead of the sandbox.
- [ ] Grant the Automation Managed Identity a higher-level role like Contributor.
- [ ] Deploy a separate AOE instance in each virtual network.

**Hint:** The solution involves moving the execution context of the runbooks away from the default shared environment.

## Question 5
The FinOps Framework documentation outlines several core principles. Which of the following best summarises one of these principles?

- [ ] Engineering teams should focus solely on performance, leaving cost to the finance department.
- [ ] All FinOps practices must be managed decentrally by individual teams for maximum agility.
- [x] Business value, including quality and speed, should drive technology and cost decisions.
- [ ] Cloud cost optimisation should be done in large, infrequent projects to minimise disruption.

**Hint:** Consider the balance between saving money and achieving business goals.

## Question 6
When using a FinOps hubs Power BI report, what is the purpose of the 'managed datasets' like 'Costs' and 'Prices'?

- [ ] They are temporary tables that are deleted after each data refresh.
- [x] They are logical datasets with versioned functions to ensure backwards compatibility across FinOps hub releases.
- [ ] They are read-only datasets provided by Microsoft that cannot be modified or extended.
- [ ] They exclusively contain raw, untransformed data directly from Cost Management exports.

**Hint:** Think about how custom reports built on this data model can avoid breaking when a new version of the hub is released.

## Question 7
To use the 'Rate optimization report' in Power BI effectively and calculate accurate savings, which two data exports are marked as 'Required'?

- [ ] Cost and usage (FOCUS) and Reservation transactions.
- [ ] Price sheet and Reservation details.
- [x] Cost and usage (FOCUS) and Price sheet.
- [ ] Reservation recommendations and Reservation details.

**Hint:** One required export provides the core spending data, while the other is needed to establish a baseline price for calculating discounts.

## Question 8
What is the conformance score calculated by Microsoft for its Cost Management FOCUS 1.2-preview dataset, and what does it account for?

- [ ] 100%, accounting for all MUST and SHOULD requirements.
- [ ] 91.7%, accounting for only the fully supported requirements.
- [x] 94%, accounting for all fully supported and half of the partially supported requirements.
- [ ] 85%, accounting only for the mandatory MUST requirements.

**Hint:** The calculation method mentioned in the conformance report is specific about how it treats requirements that are not fully met.

## Question 9
What is a key function of the 'Chargeback' page found in both the 'Invoicing and chargeback' and 'Rate optimization' Power BI reports?

- [ ] To show the total billed cost as it will appear on the final invoice for reconciliation.
- [ ] To recommend new reservation or savings plan purchases based on historical usage.
- [x] To facilitate the allocation of amortised costs to specific subscriptions or resource groups.
- [ ] To provide a list of all prices for products used during the period.

**Hint:** This page helps answer the question of 'who used what' when it comes to shared commitment discounts.

## Question 10
How does the Azure Optimization Engine (AOE) widen its scope to include a secondary Entra ID tenant using the Azure Lighthouse approach?

- [ ] By creating a new service principal and secret in the secondary tenant.
- [x] By deploying an Azure Resource Manager template in the secondary tenant that delegates access.
- [ ] By adding the secondary tenant's ID to the 'AzureOptimization_RightSizeAdditionalPerfWorkspaces' variable.
- [ ] By executing the 'Register-MultitenantAutomationSchedules.ps1' script.

**Hint:** This method is described as more secure and cost-effective, reusing the engine's existing managed identity.

## Question 11
According to the FinOps hubs data model, which database should be used for custom queries and reports to ensure long-term stability?

- [ ] The 'Ingestion' database, using the 'raw' tables directly for the most current data.
- [ ] The 'Hub' database, using the unversioned functions like 'Costs()'.
- [ ] The 'Ingestion' database, using the 'final' tables like 'Costs_final_v1_2'.
- [x] The 'Hub' database, using the versioned functions like 'Costs_v1_2()'.

**Hint:** The recommended approach uses functions that are specifically designed not to change, even when the underlying data schema is updated.

## Question 12
When converting Cost Management data to the FOCUS format, how is the 'BilledCost' column populated for usage that was covered by a reservation or savings plan?

- [ ] It uses the 'CostInBillingCurrency' value directly from the amortised cost data.
- [x] It is set to 0.
- [ ] It is calculated by multiplying 'PayGPrice' by 'Quantity'.
- [ ] It is left null, as there is no billed cost for that row.

**Hint:** Think about whether a new charge appears on your invoice for consumption that is fully covered by an existing commitment.
