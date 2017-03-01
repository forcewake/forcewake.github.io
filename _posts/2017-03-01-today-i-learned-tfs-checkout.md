---
layout: today-i-learned-post
title: "Today I Learned: Undo checkout of unmodified files in TFS"
description: "How can you Undo Checkout of Unmodified files in a batch file"
category: til
tags: [english, tfs, csharp, cmd, tfpt]
comments: true
---

The following command will undo checkout of unmodified files in TFS

{% highlight bash %}
c:\myProject> tfpt uu . /noget /recursive
{% endhighlight %}

Thanks to [stackoverflow](http://stackoverflow.com/a/544285).
