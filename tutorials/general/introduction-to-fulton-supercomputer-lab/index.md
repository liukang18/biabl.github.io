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

<iframe width="840" height="630	" src="https://www.youtube.com/embed/i1r9BxHBG0I" frameborder="0" allowfullscreen></iframe>

## Logging In

<iframe src="https://drive.google.com/file/d/0B7gwoaKa2xaTZXFBVmtlNnU3RjA/preview" width="840" height="525"></iframe>

Interaction with the supercomputer is typically performed with command line tools. The command line tools can be run via a command prompt, also known as a shell. SSH is used to establish a secure shell with the supercomputer. In general, users should log in via the hostname ssh.fsl.byu.edu.

{% highlight bash %}
ssh BYUNetID@ssh.fsl.byu.edu
{% endhighlight %}

Programs can be tested from the interactive nodes, but anything left running for more than an hour will be killed automatically.

## Installing Programs

### Insight Segmentation and Registration Toolkit (ITK)

<iframe src="https://drive.google.com/file/d/0B7gwoaKa2xaTNzhHTXFXUHRoM1U/preview" width="840" height="525"></iframe>

### dcm2niix

<iframe src="https://drive.google.com/file/d/0B7gwoaKa2xaTMW5uTHJLUDhyZnM/preview" width="840" height="525"></iframe>

### Advanced Normalization Tools (ANTs)

<iframe src="https://drive.google.com/file/d/0B7gwoaKa2xaTd0E4anllMk56Snc/preview" width="840" height="525"></iframe>

### acpcdetect

<iframe src="https://drive.google.com/file/d/0B7gwoaKa2xaTMnNmRmdCcEtOYzA/preview" width="840" height="525"></iframe>

### FreeSurfer

<iframe src="https://drive.google.com/file/d/0B7gwoaKa2xaTUjJzcHJleWRheVk/preview" width="840" height="525"></iframe>

### Convert3D

<iframe src="https://drive.google.com/file/d/0B7gwoaKa2xaTMlhkZE8xdVg0czQ/preview" width="840" height="525"></iframe>

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
