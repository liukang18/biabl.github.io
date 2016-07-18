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

Note that everything in "{}" is to be replaced. For example, \{fileName\} --> iLovePeanuts.txt

## Introduction to BYU's Supercomputer

<iframe width="840" height="630	" src="https://www.youtube.com/embed/i1r9BxHBG0I" frameborder="0" allowfullscreen></iframe>

## Logging In

Interaction with the supercomputer is typically performed with command line tools. The command line tools can be run via a command prompt, also known as a shell. SSH is used to establish a secure shell with the supercomputer. In general, users should log in via the hostname ssh.fsl.byu.edu.

{% highlight bash %}
ssh {username}@ssh.fsl.byu.edu
{% endhighlight %}

where {username} is your NetID. Programs can be tested from the interactive nodes, but anything left running for more than an hour will be killed automatically.

## Installing Programs

<iframe src="https://drive.google.com/file/d/0B7gwoaKa2xaTYXVNTGd5Z0ZPMEU/preview" width="840" height="540"></iframe>

## Update your .bash_profile

After following the instructions in the above video, your .bash_profile should have the following information. You can list the content of your .bash_profile:

{% highlight bash %}
cat ~/.bash_profile
{% endhighlight %}

The output should read:

{% highlight bash %}
# ANTs
export ANTSPATH=/fslhome/intj5/apps/ants-20160716/bin/
PATH=${ANTSPATH}:${PATH}

# acpcdetect
ARTHOME=/fslhome/intj5/apps/acpcdetect/
export ARTHOME

# FreeSurfer
export FREESURFER_HOME=/fslhome/intj5/apps/freesurfer
{% endhighlight %}
