---
layout: post
author: Naomi
tags: 
  - AFQ
  - DTI
  - "tips 'n tricks"
published: true
title: Editing AFQ_SegmentCallosum Code
---

At the end of the AFQ_SegmentCallosum.m code is a process to generate montage images of the segmented corpus callosum in each participant. If you are running this code on the Supercomputer, trying to generate figures will cause the process to abort. The only solution is to edit the code and comment out or delete this portion of the code:

<!-- more -->

### Original Code

{% highlight matlab %}
fgNames = {'CC_Occipital' 'CC_Post_Parietal' ...
   'CC_Sup_Parietal' 'CC_Motor' 'CC_Sup_Frontal' ...
   'CC_Ant_Frontal' 'CC_Orb_Frontal' 'CC_Temporal'};
AFQ_MakeFiberGroupMontage(afq, fgNames)
{% endhighlight %}

### Edited Code

{% highlight matlab %}
% fgNames = {'CC_Occipital' 'CC_Post_Parietal' ...
%    'CC_Sup_Parietal' 'CC_Motor' 'CC_Sup_Frontal' ...
%    'CC_Ant_Frontal' 'CC_Orb_Frontal' 'CC_Temporal'};
% AFQ_MakeFiberGroupMontage(afq, fgNames)
{% endhighlight %}