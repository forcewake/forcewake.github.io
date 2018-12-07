---
layout: today-i-learned-post
title: "Today I Learned: Mocking multiple named exports in Jest"
description: "Can I mock multiple named exports in jest?"
category: til
tags: [english, js, react, jest]
comments: true
---

I know only one way

For anyone who wants a Linq solution, it's:

Mock it:
{% highlight js %}
jest.mock('path/to/some/module', () => ({
    someNamedExportFromModule: jest.fn()
  }));
{% endhighlight %}

Then import it:
{% highlight js %}
import {someNamedExportFromModule} from 'path/to/some/module';
{% endhighlight %}

Setup value to return:
{% highlight js %}
someNamedExportFromModule.mockReturnValue('someValue');
{% endhighlight %}

???

You are the best.

gl hf
