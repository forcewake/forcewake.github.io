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

{% highlight csharp %}
private static string MakeValidFileName(string name)
{
    string invalidChars = new string(Path.GetInvalidFileNameChars())
    string escapedInvalidChars = Regex.Escape();
    string invalidRegex = string.Format(@"([{0}]*\.+$)|([{0}]+)", escapedInvalidChars);

    return Regex.Replace(name, invalidRegex, "_");
}
{% endhighlight %}

Working example you can find on [dotnetfiddle](https://dotnetfiddle.net/hU7dcj)
