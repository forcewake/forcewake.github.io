---
categories:
- til
comments: true
date: "2018-12-07T00:00:00Z"
description: Can I mock multiple named exports in jest?
tags:
- english
- js
- react
- jest
title: 'Today I Learned: Mocking multiple named exports in Jest'
---

I know only one way.

Mock it:
{{< highlight js >}}
jest.mock('path/to/some/module', () => ({
    someNamedExportFromModule: jest.fn()
  }));
{{< / highlight >}}

Then import it:
{{< highlight js >}}
import {someNamedExportFromModule} from 'path/to/some/module';
{{< / highlight >}}

Setup value to return:
{{< highlight js >}}
someNamedExportFromModule.mockReturnValue('someValue');
{{< / highlight >}}

???

You are the best.

gl hf
