---
layout: today-i-learned-post
title: "Today I Learned: Azure Cosmos Db: Query for Fields not defined"
description: "Query documents that don't have fields defined in Azure Cosmos Db?"
category: til
tags: [english, csharp, Azure, CosmosDb, linq, sql]
comments: true
---

There are several ways: 

## Using LINQ

For anyone who wants a Linq solution, it's:

{% highlight csharp %}
query.Where(d => !d.SomeField.IsDefined())
{% endhighlight %}

**Note:**

> To get the `IsDefined` extension method, you need to add `Microsoft.Azure.Documents.SystemFunctions` namespace

## Using SQL Query Grammar with built-in functions

`IS_DEFINED (expr)` - returns a Boolean indicating if the property has been assigned a value.

{% highlight sql %}
SELECT TOP {0} * FROM Telemetry t WHERE (is_defined(t.longitude) OR is_defined(t.latitude))
{% endhighlight %}

Thanks to [Azure Feedback](https://feedback.azure.com/forums/263030-azure-cosmos-db/suggestions/6956905-query-for-fields-not-defined) and [Azure Cosmos DB SQL syntax reference
](https://docs.microsoft.com/en-us/azure/cosmos-db/sql-api-sql-query-reference)
