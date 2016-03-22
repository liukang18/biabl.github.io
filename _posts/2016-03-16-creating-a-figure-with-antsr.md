---
excerpt_separator: "<!-- more -->"
layout: post
author: naomihunsaker
tags: 
  - "null"
published: true
title: Creating A Figure With ANTsR
---


## Quick Surface view of a brain
While creating a figure for one of the labs recent publications I developed a function that conveniently extracts a view of each side of the brain. This is using the ANTsR package and my little function can be found [here](https://github.com/Tokazama/rft/blob/master/R/fullView.R).

As an example I used the following commands to make...
```
renderSurfaceFunction(list(mask), list(roi), alphasurf = .5, smoothsval = 1.5, smoothfval = 1)
fullView("Desktop/tests/")
```
...this picture![FullView.png]({{site.baseurl}}/media/FullView.png)

![]({{site.baseurl}}/media/FullView.png)
