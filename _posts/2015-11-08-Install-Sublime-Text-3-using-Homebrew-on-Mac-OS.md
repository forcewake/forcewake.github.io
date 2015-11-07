---
layout: post
title: "Install Sublime Text 3 using Homebrew on Mac OS"
description: "Short how-to about Homebrew and Sublime Text 3"
category: articles
tags: [macos, software, homebrew, sublime, english]
comments: true
---

It's pretty easy to install it but for man who is moving from Windows to Mac OS it is rather hard to change mind.

I did not have brew cask installed so I had to install it first, so these were the steps I followed:

{% highlight bash %}
brew install caskroom/cask/brew-cask
brew tap caskroom/versions
brew cask install sublime-text3
{% endhighlight %}
