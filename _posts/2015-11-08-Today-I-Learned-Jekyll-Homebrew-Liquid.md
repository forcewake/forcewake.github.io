---
layout: post
title: "Today I Learned: Homebrew, Jekyll and Liquid"
description: "Short how-to about Homebrew and Sublime Text 3, templates in Liquid and Jekyll bundle"
category: til
tags: [english, nuget, software, homebrew, sublime, liquid, jekyll]
comments: true
---

I've decide to start writing in my blog..
And I faced with several issues and strange things in the tools and frameworks.

## How-to install Sublime Text 3 with Homebrew
It's pretty easy to install it but for man who is moving from Windows to Mac OS it is rather hard to change mind.

I did not have brew cask installed so I had to install it first, so these were the steps I followed:

{% highlight bash %}
brew install caskroom/cask/brew-cask
brew tap caskroom/versions
brew cask install sublime-text3
{% endhighlight %}

## Jekyll and Liquid

### How do I chain if statements in Jekyll?
In Jekyll/Liquid else-if is spelt `elsif`, i.e.:
{% highlight html %}
{% raw %} 
{% if post.category == "speaking" %}
  <i class="fa fa-microphone"></i> 
{% elsif post.category == "til" %}
  <i class="fa fa-hand-spock-o"></i>
{% else %}
  <i class="fa fa-hand-paper-o"></i>
{% endif %}
{% endraw %} 
{% endhighlight %}

### How to use Jekyll code in (inline) code highlighting?
Use `{% raw %}raw{% endraw %}` and `{% raw %}endraw{% endraw %}` tags in highlighting block.

### Cannot load such file -- jekyll-sitemap (LoadError)
It works using bundle exec. Probably problem described [here](https://github.com/mmistakes/skinny-bones-jekyll/issues/10)
{% highlight bash %}
bundle exec jekyll build
bundle exec jekyll serve
{% endhighlight %}

## Bundle command not found mac
Use `gem install bundler`, Luke

## Reinstall nuget packages in project
Ever been in a situation where you wanted to have all nuget packages of a project to be kind of “refreshed”?
{% highlight bash %}
Update-Package –reinstall -project MySuperProject.Web
{% endhighlight %}
