---
layout: today-i-learned-post
title: "Today I Learned: Nuget commands"
description: "Some key point around issues with Ninject in OWIN WebApi application"
category: til
tags: [english, dotnet, nuget]
comments: true
---

The following command will update all packages in every project to the latest version available from nuget.org.

{% highlight bash %}
Update-Package
{% endhighlight %}

You can also restrict this down to one project.

{% highlight bash %}
Update-Package -Project YourProjectName
{% endhighlight %}

If you want to reinstall the packages to the same versions as were previously installed then you can use the `-reinstall` argument with `Update-Package` command.

{% highlight bash %}
Update-Package -reinstall
{% endhighlight %}

You can also restrict this down to one project.

{% highlight bash %}
Update-Package -reinstall -Project YourProjectName
{% endhighlight %}

The `-reinstall` option will first uninstall and then install the package back again into a project.

Thanks to [stackoverflow](http://stackoverflow.com/a/6882750)
