---
layout: today-i-learned-post
title: "Today I Learned: Sanitize File Name in C#"
description: ""
category: til
tags: [english, dotnet]
comments: true
---

A short update about safe file names in C#.

## Sanitize File Name in C#
To clean up a file name you could do this

<iframe width="100%" height="475" src="https://dotnetfiddle.net/Widget/hU7dcj" frameborder="0"></iframe>

{% highlight csharp %}
private static string MakeValidFileName(string name)
{
	string invalidChars = Regex.Escape(new string(Path.GetInvalidFileNameChars()));
	string invalidRegStr = string.Format(@"([{0}]*\.+$)|([{0}]+)", invalidChars);

	return Regex.Replace(name, invalidRegStr, "_");
}
{% endhighlight %}
