---
layout: post
title: "Oh noes, acpcdetect Failed!"
author: naomi
tags: 
  - acpcdetect
  - "tips 'n tricks"
published: true
---



Occasionally, *acpcdetect* might not accurately find and adjust the images along the anterior and posterior commissure.  This can happen if there's a lot of damage in the brain and the ventricles are enlarged. There are several things you can do to help *acpcdetect*.

<!-- more -->

## Adjust Search Radius

By default the search radius for the VSPS is 50 mm. You can change this by adding the option `-rvsps <r>`.

The default search radii for the anterior and posterior commissure are 15 mm. You can change each of these by adding the options `-rac <r>` and `-rpc <r>`.

Adjusting the search radius does not always work though.

## Specify the Precise Location

You can also just tell the program where VSPS, AC, and PC are exactly located. To tell *acpcdetect* where the VSPS is located, add the following option: `-VSPS <x> <y> <z>`.  

***The best program to use is ITK-Snap to determine coordinates.***

![VSPS.png]({{site.baseurl}}/media/VSPS.png){: .img-responsive }

To specify the location of the anterior commissure: `-AC <x> <y> <z>`.

![AC.png]({{site.baseurl}}/media/AC.png){: .img-responsive }

To specify the location of the posterior commissure: `-PC <x> <y> <z>`.

![PC.png]({{site.baseurl}}/media/PC.png){: .img-responsive }

With these methods, you will be able to fix any problems with *acpcdetect*.
