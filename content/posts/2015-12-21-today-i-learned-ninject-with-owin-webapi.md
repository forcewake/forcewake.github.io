---
categories:
- til
comments: true
date: "2015-12-21T00:00:00Z"
description: Some key point around issues with Ninject in OWIN WebApi application
tags:
- english
- dotnet
- Ninject
- WebApi
- nuget
title: 'Today I Learned: Ninject in OWIN WebApi application'
---

This chapter explains how to setup an application in case you want to use the OWIN (Open Web Interface for .NET) interface to be able to move your application between OWIN supporting hosts without making code changes.

## Requirements

### Version

> You will need Version 3.2.4 or newer, otherwise you will get a `Ninject.ActivationException` regarding `HttpConfiguration`.

### Installation via nuget

First you have to add a reference to Ninject.Web.Common.OwinHost and Ninject.Web.WebApi.OwinHost either by adding the reference manually or installing the corresponding NuGet Package.

> Right now in nuget `Ninject.Web.WebApi.OwinHost` has several missing dependencies, so we will install this package manual, using Package Manager Console:

{{< highlight bash >}}
Install-Package Ninject.Web.WebApi.OwinHost -Version 3.2.4 -DependencyVersion Highest
{{< / highlight >}}

## Using

You can initialize the Ninject middleware with the extension method UseNinjectMiddleware and register the WebApi through Ninject with the extension method UseNinjectWebApi. The call to "app.UseWebApi(config)" should be replaced with "app.UseNinjectWebApi(config)". start the WebApi you have to pass a Configuration object. This configuration is specific to the WebApi technology. Please read the documentation for this technologies to get more informations about how to configure a WebApi service.

{{< highlight csharp >}}
public void Configuration(IAppBuilder app)
{
    var webApiConfiguration = new HttpConfiguration();
    webApiConfiguration.Routes.MapHttpRoute(
        name: "DefaultApi",
        routeTemplate: "{controller}/{id}",
        defaults: new { id = RouteParameter.Optional, controller = "values" });

    app.UseNinjectMiddleware(CreateKernel).UseNinjectWebApi(webApiConfiguration);
}

private static StandardKernel CreateKernel()
{
    var kernel = new StandardKernel();
    kernel.Load(Assembly.GetExecutingAssembly());
    return kernel;
}
{{< / highlight >}}