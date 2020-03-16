---
categories:
- til
comments: true
date: "2016-10-28T00:00:00Z"
description: Some key point around issues with Ninject in OWIN WebApi application
tags:
- english
- dotnet
- nuget
title: 'Today I Learned: Nuget commands'
---

The following command will update all packages in every project to the latest version available from nuget.org.

{{< highlight bash >}}
Update-Package
{{< / highlight >}}

You can also restrict this down to one project.

{{< highlight bash >}}
Update-Package -Project YourProjectName
{{< / highlight >}}

If you want to reinstall the packages to the same versions as were previously installed then you can use the `-reinstall` argument with `Update-Package` command.

{{< highlight bash >}}
Update-Package -reinstall
{{< / highlight >}}

You can also restrict this down to one project.

{{< highlight bash >}}
Update-Package -reinstall -Project YourProjectName
{{< / highlight >}}

The `-reinstall` option will first uninstall and then install the package back again into a project.

Thanks to [stackoverflow](http://stackoverflow.com/a/6882750)
