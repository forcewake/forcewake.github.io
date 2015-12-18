---
layout: today-i-learned-post
title: "Today I Learned: SharpRepository"
description: ""
category: til
tags: [english, dotnet, SharpRepository]
comments: true
---

I absolutely forgot about the blog. My bad. Let's add something interesting about C#.

## SharpRepository
[SharpRepository](https://www.nuget.org/packages/SharpRepository.Repository/) is a generic repository written in C# which includes support for various relational, document and object databases including Entity Framework, RavenDB, MongoDB, CouchDB and Db4o. SharpRepository includes Xml and InMemory repository implementations as well. SharpRepository offers built-in caching options for AppFabric, memcached and the standard System.Runtime.Caching. SharpRepository also supports Specifications, FetchStrategies, Batches and Traits.

To install SharpRepository, run the following command in the Package Manager Console

{% highlight bash %}
PM> Install-Package SharpRepository.Repository
{% endhighlight %}

All information about SharpRepository you can find on [github](https://github.com/SharpRepository/SharpRepository)

## How to use Include() to include related entities?
One of the most interesting question was about Entity Framework and `Include` functionality. For example, you have `CurrencyRate` class:

{% highlight csharp %}
public class CurrencyRate
{
    public int Id { get; set; }
	
    public string Name { get; set; }

    public int BranchBankId { get; set; }

    [ForeignKey("BranchBankId")]
    public BankBranch BankBranch { get; set; }
}
{% endhighlight %}

And you need to get information about the `BankBranch`. 
There are 2 general ways to query the repository:

1. Use a predicate in query
2. Use a `Specification` object that holds predicate.

The equivalent of the EF Include statements are set in a `Specification` object and called a `FetchStrategy`.

{% highlight csharp %}
var spec = new Specification<CurrencyRate>(b => b.Name == name);

spec.FetchStrategy.Include(b => b.BankBranch);
CurrencyRate currencyRate;
if (currencyRateRepository.TryFind(spec, out currencyRate))
{
	// ... some logic with BankBranch
}
{% endhighlight %}
