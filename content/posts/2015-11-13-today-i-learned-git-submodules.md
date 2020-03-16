---
categories:
- til
comments: true
date: "2015-11-13T00:00:00Z"
description: ""
tags:
- english
- git
title: 'Today I Learned: Git Submodules'
---

A short update about submodules in git.

## Git submodules
Let’s start by adding an existing Git repository as a submodule of the repository that we’re working on. To add a new submodule you use the git submodule add command with the absolute or relative URL of the project you would like to start tracking.

{{< highlight bash >}}
git submodule add https://github.com/forcewake/CommandPipeline
{{< / highlight >}}

By default, submodules will add the subproject into a directory named the same as the repository.

More information you can find in [7.11 Git Tools - Submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules)
