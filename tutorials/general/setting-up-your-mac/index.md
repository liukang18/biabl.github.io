---
layout: tutorials
title: Setting Up Your Mac
author: naomi
comments: true
date: 2016-07-25
---

## Objectives

After you complete this section, you should be able to:

1. Install Homebrew
2. Brew install wget and cmake
3. Change Terminal.app preferences
4. Install Mango
5. Install text editing application
6. Install Quicklook Plugin for NIfTI images

## Version

I am currently running OS X El Capitan (10.11.5) on a MacBook Pro (Retina, 15-inch, Mid 2014).

## Homebrew

Paste this in a Terminal window.

{% highlight bash %}
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
{% endhighlight %}

The script explains what it will do and then pauses before it does it.

## Brew Install

Homebrew installs the stuff you need that Apple didn’t like wget:

{% highlight bash %}
brew install wget
{% endhighlight %}

And cmake:

{% highlight bash %}
brew install cmake
{% endhighlight %}

## Mango

The best program for viewing MR images is Mango Imaging and can be installed on Mac OS X, Windows (64-bit), or Linux (64-bit). Grab the latest version:

[http://ric.uthscsa.edu/mango/mango.html](http://ric.uthscsa.edu/mango/mango.html)

## TextEditor

If you are on a Mac OS X, TextWrangler is a great tool, but more and more applications are coming out that may actually be better for text editing:

[https://atom.io](https://atom.io)

## Quicklook Plugin

[http://dti-tk.sourceforge.net/pmwiki/pmwiki.php?n=QuicklookPlugin.Main](http://dti-tk.sourceforge.net/pmwiki/pmwiki.php?n=QuicklookPlugin.Main)

On Mac OS X, press the spacebar to quickly look at files.
