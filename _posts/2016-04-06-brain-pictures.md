---
excerpt_separator: "<!-- more -->"
layout: post
author: zachchristensen
tags: 
  - R
published: true
title: Brain Pictures
---
If you're part of the same generation as I am you yell at the microwave because 30 seconds is a ludicrous amount of time to make a personal cake bowl (I want cake and I want it now!). Similarly the hassle that is involved in loading a nifti file into mango, rendering a surface, and adjusting the the range of values displayed is unbearable (don't even get me started on using the correct color gradient).

This is why I made the following function:

<!-- more -->

https://github.com/Tokazama/rft/blob/master/R/renderFunctional.R

I loaded up an image and used the following code.

```
myscene <- renderFunctional(list(img))
rgl.snapshot("Desktop/funimg.png", fmt = "png", top = TRUE)
```

The `rgl.snapshot` function just takes a picture of the the interactive brain in the Quartz window, giving you:
![funimg.png]({{site.baseurl}}/media/funimg.png)

It's not a super quick function, but in my experience I go through several different views/colors/perspectives of an image before I find the right one for a publication. Now it's easy to right up a script that will do it all in several minutes without having to personally fiddle around with settings. Now I'll finally have enough free time to eat the personal cake bowl.
