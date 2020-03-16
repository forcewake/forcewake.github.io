---
categories:
- til
comments: true
date: "2017-03-01T00:00:00Z"
description: How can you Undo Checkout of Unmodified files in a batch file
tags:
- english
- tfs
- csharp
- cmd
- tfpt
title: 'Today I Learned: Undo checkout of unmodified files in TFS'
---

The following command will undo checkout of unmodified files in TFS

{{< highlight bash >}}
c:\myProject> tfpt uu . /noget /recursive
{{< / highlight >}}

Thanks to [stackoverflow](http://stackoverflow.com/a/544285).
