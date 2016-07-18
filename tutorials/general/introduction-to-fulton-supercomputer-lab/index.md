---
layout: tutorials
title: Introduction to Fulton Supercomputer Lab
author: naomi
comments: true
date: 2015-12-09
---

## Objectives

After you complete this section, you should be able to:

1. Establish a secure shell with the supercomputer
2. Copy the applications from the byustudent group directory
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

<iframe src="https://drive.google.com/file/d/0B7gwoaKa2xaTYXVNTGd5Z0ZPMEU/preview" width="840" height="630"></iframe>

### Additional Classroom Materials

* [Lecture](presentation)
* [Assignment](assignment)
