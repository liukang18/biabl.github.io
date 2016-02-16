---
layout: post
title: ANTs Installation Bug
author: trevorhuff
tags: 
  - supercomputer
  - bug
  - ANTs
published: true
excerpt_separator: "<!-- more -->"
---

There's a bug with how GCC is installed on the supercomputer, which prevents installation of ANTs. Luckily there's a solution.

<!-- more -->

Make sure you start with a completely fresh install. Remove the `ANTs` and `antsbin` directories:

{% highlight bash %}
cd ~/apps/
rm -rf ANTs
rm -rf antsbin
{% endhighlight %}

Follow the directions provided by ANTs:

{% highlight bash %}
cd ~/apps/
git clone git://github.com/stnava/ANTs.git
mkdir ants-20151207
cd ants-20151207
ccmake ../ANTs
{% endhighlight %}

In CMAKE, type "c" to load the configuration information and then type "t" to toggle to advanced mode. You want to edit the option, "CMAKE\_EXE\_LINKER\_FLAGS" which by default is blank, but needs to be `-fuse-ld=bfd` instead. Press "ENTER" to edit the option.

![](/images/2015-12-07-ants-installation-bug/screenshot-image1.png)

Type "c" and then "g" and then exit back to the terminal by typing "q".

Compile your code:

{% highlight bash %}
make -j 4
{% endhighlight %}

and wait awhile.

Once ANTs is installed, remember to copy the scripts to your ANTs bin directory and set your environmental variables:

{% highlight bash %}
cp -v ~/apps/ANTs/Scripts/* ~/apps/ants-20151207/bin/
echo "export ANTSPATH=/fslhome/<USERNAME>/antsbin-20151207/bin/" >> ~/.bash_profile
echo "export PATH=${ANTSPATH}:${PATH}" >> ~/.bash_profile
{% endhighlight %}