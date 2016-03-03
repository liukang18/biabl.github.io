---
excerpt_separator: "<!-- more -->"
layout: post
author: naomihunsaker
tags: 
  - "tips "
published: true
title: Update Code on Supercomputer Hack
---


I had a "duh" moment the other day to improve the process between updating code on my Github Gist and updating code on the Supercomputer. It seems obvious, but if you have a pipeline developed for processing data on gist (see below), you can easily use that directory on the supercomputer. I was able to clone my gist on the supercomputer: 

```{bash}
git clone git@gist.github.com:517ebd426bc3a8a322a4.git MIOS-CC/
```
Any time that I need to make updates, I make updates to the gist and then on the supercomputer:

```{bash}
cd ~/MIOS-CC
git pull
```
This is a great way to keep track of changes to your code, instead of changing code directly in the terminal window. Doing a process like this will be in line with NIH protocols for lab documentation.
