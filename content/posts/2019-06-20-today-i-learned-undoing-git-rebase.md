---
categories:
- til
comments: true
date: "2019-06-20T00:00:00Z"
description: Does anybody know how to easily undo a git rebase?
tags:
- english
- git
title: 'Today I Learned: Undoing a git rebase'
---

The easiest way would be to find the head commit of the branch as it was immediately before the rebase started in the reflog...

{{< highlight bash >}}
git reflog
{{< / highlight >}}

and to reset the current branch to it (with the usual caveats about being absolutely sure before reseting with the `--hard` option).

Suppose the old commit was `HEAD@{5}` in the ref log:

{{< highlight bash >}}
git reset --hard HEAD@{5}
{{< / highlight >}}

In Windows, you may need to quote the reference:

{{< highlight bash >}}
git reset --hard "HEAD@{5}"
{{< / highlight >}}

You can check the history of the candidate old head by just doing a git log `HEAD@{5}` (Windows: `git log "HEAD@{5}"`).

Copied from [CB Bailey answer](https://stackoverflow.com/users/19563/cb-bailey) from https://stackoverflow.com/a/135614
