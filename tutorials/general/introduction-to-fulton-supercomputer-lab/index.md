---
layout: tutorials
title: Introduction to Fulton Supercomputer Lab
author: naomi
comments: true
date: 2016-07-18
---

## Objectives

After you complete this section, you should be able to:

1. Establish a secure shell with the supercomputer
2. Install ANTs, acpcdetect, ITK, Convert3D, FreeSurfer, and dcm2nii
3. Permanently set your environmental variables

## Introduction to BYU's Supercomputer

<div class="embed-container">
  <iframe width="840" height="630	" src="https://www.youtube.com/embed/i1r9BxHBG0I" frameborder="0" allowfullscreen></iframe>
</div>

## Logging In

<div class="embed-container">
  <iframe src="https://player.vimeo.com/video/179372014?byline=0&portrait=0" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>
</div>

Interaction with the supercomputer is typically performed with command line tools. The command line tools can be run via a command prompt, also known as a shell. SSH is used to establish a secure shell with the supercomputer. In general, users should log in via the hostname ssh.fsl.byu.edu.

{% highlight bash %}
ssh BYUNetID@ssh.fsl.byu.edu
{% endhighlight %}

Programs can be tested from the interactive nodes, but anything left running for more than an hour will be killed automatically.

## Installing Programs

### Insight Segmentation and Registration Toolkit (ITK)

<div class="embed-container">
  <iframe src="https://player.vimeo.com/video/179385120?byline=0&portrait=0" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>
</div>

### dcm2niix

<div class="embed-container">
  <iframe src="https://player.vimeo.com/video/179385119?byline=0&portrait=0" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>
</div>

### Advanced Normalization Tools (ANTs)

<div class="embed-container">
  <iframe src="https://player.vimeo.com/video/179385121?byline=0&portrait=0" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>
</div>

### acpcdetect

<div class="embed-container">
  <iframe src="https://player.vimeo.com/video/179372009?byline=0&portrait=0" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>
</div>

### FreeSurfer

<div class="embed-container">
  <iframe src="https://player.vimeo.com/video/179385122?byline=0&portrait=0" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>
</div>

### Convert3D

<div class="embed-container">
  <iframe src="https://player.vimeo.com/video/179385123?byline=0&portrait=0" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>
</div>

## Update your .bash_profile

After following the instructions in the above video, your .bash_profile should have the following information. You can list the content of your .bash_profile:

{% highlight bash %}
cat ~/.bash_profile
{% endhighlight %}

The output should read:

{% highlight bash %}
# ANTs
export ANTSPATH=/fslhome/BYUNetID/apps/ants/bin/
PATH=${ANTSPATH}:${PATH}

# acpcdetect
ARTHOME=/fslhome/BYUNetID/apps/art/
export ARTHOME

# FreeSurfer
export FREESURFER_HOME=/fslhome/BYUNetID/apps/freesurfer
{% endhighlight %}

## Class Slides

<div class="embed-container">
  <iframe src="http://slides.com/njhunsak/deck/embed"scrolling="no" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>
</div>
