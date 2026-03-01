# FinOps Flashcards

## Card 1

**Q:** What are the three phases of the FinOps lifecycle as defined by the FinOps Framework?

**A:** The three phases are Inform, Optimize, and Operate.

---

## Card 2

**Q:** According to FinOps principles, who should take ownership for their technology usage?

**A:** Everyone should take ownership for their cloud resource usage and optimisation.

---

## Card 3

**Q:** In the context of the FinOps Framework, what does the 'Inform' phase focus on?

**A:** The 'Inform' phase focuses on delivering cost visibility and creating shared accountability through allocation, benchmarking, budgeting, and forecasting.

---

## Card 4

**Q:** The FinOps principle that involves centralising management of practices for consistency, automation, and rate negotiations is known as 'FinOps should be enabled _____'.

**A:** centrally

---

## Card 5

**Q:** What is the primary purpose of the FinOps Open Cost and Usage Specification (FOCUS)?

**A:** It provides a standardised, open-source specification for cloud cost and usage data to simplify multi-cloud reporting and analysis.

---

## Card 6

**Q:** In the FOCUS specification, what is the mandatory prefix for all custom (provider-defined) columns?

**A:** Custom columns must be prefixed with 'x_' to distinguish them from standard FOCUS columns.

---

## Card 7

**Q:** According to FOCUS column handling rules, what casing style must be used for Column IDs?

**A:** Column IDs must use Pascal case.

---

## Card 8

**Q:** What does the 'BilledCost' column in a FOCUS dataset represent?

**A:** It represents a charge serving as the basis for invoicing, inclusive of all reduced rates and discounts but excluding amortisation of upfront charges.

---

## Card 9

**Q:** What is the difference between 'Actual' and 'Amortized' cost in Microsoft Cost Management?

**A:** 'Actual' cost shows charges as they appear on the invoice, while 'Amortized' cost shows the effective cost by spreading purchases like reservations over their term.

---

## Card 10

**Q:** When configuring an AI agent for a FinOps hub, what protocol is used to connect and query data in Azure Data Explorer?

**A:** FinOps hubs use the Model Context Protocol (MCP) to connect to and query data.

---

## Card 11

**Q:** What is a prerequisite for configuring an AI agent to connect to a FinOps hub?

**A:** You must have deployed a FinOps hub instance with Data Explorer and have database viewer (or greater) access to the Hub and Ingestion databases.

---

## Card 12

**Q:** In a FinOps hub data model, which database should be used for querying data and which should be avoided for general use?

**A:** You should always use the 'Hub' database for querying and avoid working directly with the tables and functions in the 'Ingestion' database.

---

## Card 13

**Q:** What is a 'managed dataset' in the context of FinOps hubs?

**A:** A managed dataset is a logical dataset backed by resources like a storage folder, Data Explorer tables, versioned functions, and a Power BI table.

---

## Card 14

**Q:** The unversioned function 'Costs()' in a FinOps hub calls which versioned function to retrieve the latest data schema?

**A:** It calls the latest versioned function, for example 'Costs_v1_2()', which then queries all underlying data tables.

---

## Card 15

**Q:** What is the purpose of the Azure Optimization Engine (AOE)?

**A:** The AOE is a set of Azure Automation runbooks that collect, ingest, and analyse data to provide cost optimisation recommendations.

---

## Card 16

**Q:** How can you widen the scope of the Azure Optimization Engine (AOE) to include other subscriptions?

**A:** You can grant the Reader role to the AOE's Managed Identity on other subscriptions or at a higher-level Management Group.

---

## Card 17

**Q:** In the Azure Optimization Engine (AOE), what is the purpose of the `AzureOptimization_PerfPercentileCpu` variable?

**A:** It sets the percentile for CPU metrics aggregations used in the virtual machine right-size fit score algorithm.

---

## Card 18

**Q:** What is the key difference between using a service principal and Azure Lighthouse to extend AOE to another tenant?

**A:** A service principal uses a secret for authentication, whereas Azure Lighthouse reuses the engine's more secure managed identity.

---

## Card 19

**Q:** The 'Rate optimization report' in the FinOps toolkit summarises savings from what type of discounts?

**A:** It summarises savings from commitment discounts, such as reservations and savings plans.

---

## Card 20

**Q:** What does the 'Effective Savings Rate (ESR)' KPI in the Rate optimization report measure?

**A:** ESR compares total savings to the list cost to calculate an overall percentage of savings from all discounts.

---

## Card 21

**Q:** What is the purpose of the 'Chargeback' page in the FinOps toolkit's Rate optimization report?

**A:** It helps facilitate chargeback of commitment discount costs at a subscription, resource group, or resource level.

---

## Card 22

**Q:** The 'Invoicing and chargeback report' in Power BI requires which Cost Management export as a mandatory prerequisite?

**A:** It requires the 'Cost and usage (FOCUS)' dataset export.

---

## Card 23

**Q:** In the 'Invoicing and chargeback report', which cost metric is used on the 'Chargeback' page?

**A:** The 'Chargeback' page uses effective (amortised) cost rather than billed cost.

---

## Card 24

**Q:** In FinOps hubs, the 'Costs_raw' table supports FOCUS 1.0 data ingestion from which five major cloud providers?

**A:** It supports data ingestion from Microsoft Cloud, Alibaba Cloud, Amazon Web Services (AWS), Google Cloud Platform (GCP), and Oracle Cloud Infrastructure (OCI).

---

## Card 25

**Q:** What is the function of the `datestring(start: datetime, [end: datetime])` KQL function in a FinOps hub?

**A:** It returns a formatted, human-readable date or date range string that is as short as possible (e.g., 'Jan 1-15').

---

## Card 26

**Q:** What information does the `numberstring(num: double, [abbrev: bool])` KQL function provide?

**A:** It converts a number into a formatted and optionally abbreviated string (e.g., 1,234,567 becomes '1.23M').

---

## Card 27

**Q:** In the FinOps toolkit's open data, what is the purpose of the 'Pricing units' dataset?

**A:** It provides a list of `UnitOfMeasure` values and maps them to their distinct units and block sizes to help normalise and parse pricing data.

---

## Card 28

**Q:** In the FOCUS conformance report, what is the difference between a 'MUST' and a 'SHOULD' requirement?

**A:** 'MUST' requirements are mandatory for all data providers, while 'SHOULD' requirements are strong recommendations.

---

## Card 29

**Q:** According to FOCUS discount handling rules, how must purchased discounts like commitment discounts be treated?

**A:** Purchased discounts must be amortised.

---

## Card 30

**Q:** If a commitment discount is not fully utilised, what must the data provider include in the FOCUS dataset?

**A:** The provider must include a row representing the unused portion, with `CommitmentDiscountStatus` set to 'Unused'.

---

## Card 31

**Q:** In the FOCUS specification, the `Tags` column must contain data in what format?

**A:** The `Tags` column must contain a serialised JSON string consistent with the ECMA 404 definition of an object.

---

## Card 32

**Q:** The FOCUS `SubAccountId` column maps to which concept in the Microsoft Cloud?

**A:** It maps to a Microsoft Cloud subscription ID.

---

## Card 33

**Q:** To convert Cost Management data to FOCUS, you need which two cost datasets?

**A:** You need both the actual and the amortised cost datasets.

---

## Card 34

**Q:** When converting Cost Management data, how is the FOCUS `ChargeCategory` derived from the `ChargeType` column?

**A:** Values like 'Usage' and 'Purchase' are kept, while 'UnusedReservation' becomes 'Usage' and 'Refund' becomes 'Purchase'.

---

## Card 35

**Q:** Which Azure tool is used to explore resource configuration, changes, and relationships across subscriptions?

**A:** Azure Resource Graph (ARG) is used for this purpose.

---

## Card 36

**Q:** Which Azure monitoring tool helps analyse resource usage metrics, logs, and traces?

**A:** Azure Monitor is the tool used to analyse these operational metrics.

---

## Card 37

**Q:** What does the 'FinOps and intersecting frameworks' capability refer to?

**A:** It refers to integrating FinOps practices with other organisational frameworks like ITAM, ITSM, and IT Security.

---

## Card 38

**Q:** What is the primary function of the 'Resources Inventory' workbook provided by the Azure Optimization Engine?

**A:** It reports on the distribution of the most relevant Azure resource types across different perspectives and their historical evolution.

---

## Card 39

**Q:** The 'Benefits Simulation' workbook in AOE allows for simulations of savings plans and reservations based on what type of historical usage?

**A:** It bases simulations on historical on-demand Virtual Machines usage.

---

## Card 40

**Q:** In the custom deployment options for a FinOps hub, what is the security implication of choosing the 'Private' networking access option?

**A:** Choosing 'Private' access blocks all external access to storage and Data Explorer, which can enhance security but may incur additional costs for private endpoints.

---

## Card 41

**Q:** The 'Purchases' page in the FinOps toolkit Power BI reports shows the cost of new commitment discounts as what type of cost?

**A:** It shows the 'Billed cost' as it would appear on an invoice, which is different from the amortised cost shown on other pages.

---

## Card 42

**Q:** What does the `ifempty(value: dynamic, defaultValue: dynamic)` KQL function do in a FinOps hub?

**A:** It returns the specified default value if the input value is empty or null.

---

## Card 43

**Q:** The `CommitmentDiscountUsage` managed dataset in a FinOps hub is transformed from which Microsoft Cost Management export schema?

**A:** It is transformed from the reservation details export schemas for EA and MCA accounts.

---

## Card 44

**Q:** What information does the `parse_resourceid(resourceId: string)` KQL function extract from an Azure resource ID?

**A:** It parses the resource ID to extract attributes like the name, type, resource group, and subaccount ID.

---

## Card 45

**Q:** What is the purpose of the 'Services' table in a FinOps hub's Data Explorer instance?

**A:** The 'Services' table, populated from an open data file, is used to facilitate data cleansing by mapping resource types to service categories.

---

## Card 46

**Q:** In the Azure Optimization Engine, the 'Identities and Roles' workbook reports on Microsoft Entra ID objects and their roles across which two scopes?

**A:** It reports on roles across the tenant and Azure resources.

---

## Card 47

**Q:** An Azure Resource Graph query to find idle SQL databases would likely analyse which property of the databases?

**A:** It would typically analyse the database names for indicators that they are old, in development, or used for testing.

---

## Card 48

**Q:** In the FOCUS specification, what is the rule for date/time values regarding time zones?

**A:** All date/time values must be in UTC (Coordinated Universal Time) to avoid ambiguity.

---

## Card 49

**Q:** The FOCUS column 'ChargeDescription' is derived from which column in the Cost Management dataset?

**A:** It is derived from the `ProductName` column.

---

## Card 50

**Q:** Which capability domain in the FinOps Framework includes 'Rate optimization' and 'Workload optimization'?

**A:** These capabilities are part of the 'Optimize usage and cost' domain.

---

## Card 51

**Q:** A key performance indicator (KPI) found in the Rate optimization report is 'Utilization', which shows what?

**A:** Utilization shows the percentage of your current commitments that were used during the selected period.

---

## Card 52

**Q:** What is the simplest way to get started with an AI-powered FinOps hub according to the documentation?

**A:** The simplest way is with GitHub Copilot Agent mode in VS Code.

---

## Card 53

**Q:** When querying a FinOps hub with an AI agent, how can you identify resource groups with the most cost?

**A:** You can ask a natural language question like 'What are my top 10 resource groups by cost?'.

---

## Card 54

**Q:** Which built-in Azure Data Explorer functionality is used by an AI agent to identify cost anomalies in a FinOps hub?

**A:** It uses the built-in Data Explorer anomaly detection functionality.

---

## Card 55

**Q:** In the FOCUS conformance report, the `AvailabilityZone` column is noted as 'Does Not Support' for Cost Management datasets. Why?

**A:** Availability zones are not available in any Cost Management cost and usage dataset.

---

## Card 56

**Q:** What does the FOCUS requirement `DH4` state about purchased discounts like commitment discounts?

**A:** It states that purchased discounts MUST be amortised.

---

## Card 57

**Q:** How does the FinOps hub data model ensure backwards compatibility across different releases?

**A:** It uses a specific schema version aligned to a FOCUS version and provides versioned functions (e.g., Costs_v1_0()) that transform data to older schemas.

---

## Card 58

**Q:** What is the purpose of the `ftk_Storage([datasetType])` function in Power BI reports for FinOps hubs?

**A:** It reads data from Azure DataLake Storage and handles differences between Cost Management export types and FinOps hubs versions.

---

## Card 59

**Q:** What is the source for the 'ResourceTypes' table in FinOps hubs?

**A:** The 'ResourceTypes' table is populated from the 'Resource types' open data file provided by the FinOps toolkit.

---

## Card 60

**Q:** When customising the Azure Optimization Engine, what must you do if you change the runbook execution context to a Hybrid Worker?

**A:** You must always use the `DoPartialUpgrade` flag when upgrading AOE, or you will lose the runbook schedule settings.

---

## Card 61

**Q:** The `Reservation recommendations` page in the Rate optimization report uses what kind of data to identify potential savings?

**A:** It uses historical usage patterns from exported Cost Management data.

---

## Card 62

**Q:** In the Cost Management to FOCUS conversion table, how is the `RegionId` generated?

**A:** The `RegionId` is generated by taking the `RegionName`, making it lowercase, and removing spaces.

---

## Card 63

**Q:** In the FinOps Framework, what type of stakeholder is a 'Product owner' considered?

**A:** A 'Product owner' is considered a core stakeholder.

---

## Card 64

**Q:** What does the `deltastring()` KQL function in a FinOps hub return?

**A:** It returns the percentage difference between two numbers as a formatted string, optionally with arrows.

---
