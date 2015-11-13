---
layout: today-i-learned-post
title: "Today I Learned: Git Submodules"
description: ""
category: til
tags: [english, git]
comments: true
---

A short update about submodules in git.

{% include _toc.html %}

## Git submodules
Let’s start by adding an existing Git repository as a submodule of the repository that we’re working on. To add a new submodule you use the git submodule add command with the absolute or relative URL of the project you would like to start tracking.

{% highlight bash %}
git submodule add https://github.com/forcewake/CommandPipeline
{% endhighlight %}

By default, submodules will add the subproject into a directory named the same as the repository.

More information you can find in [7.11 Git Tools - Submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules)
