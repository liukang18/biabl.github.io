---
layout: post
title: buildtemplateparallel.sh Bug
author: naomihunsaker
tags: 
  - supercomputer
  - bug
  - ANTs
  - buildtemplateparalelle.sh
published: true
excerpt_separator: "<!-- more -->"
---

When trying to run buildtemplateparallel.sh, I ran into several problems. Here are a list of changes I had to make. Here's my code to run buildtemplateparallel.sh:

<!-- more -->

{% highlight bash %}
cd /fslhome/intj5/compute/templates/SOBIK/data
~/apps/ants-20151207/bin/buildtemplateparallel.sh \
-d 3 \
-o sobik \
-c 5 \
*.nii.gz
{% endhighlight %}

## Problem 1

There was not a *qscript* variable after the `elif` command on line 1237.

{% highlight bash %}
elif [[ $DOQSUB -eq 5 ]]; then
      qscript="job_${count}_${i}.sh"
      echo "#!/bin/sh" > ${qscript}
{% endhighlight %}

## Problem 2

Next issue was the lack of specifying the amount of memory required for the sbatch submission. The FSL supercomputer requires memory listed, `--mem-per-cpu=32768M`. Here's how I fixed the code on line 1242 (previously 1241 before fixing Problem #1).

{% highlight bash %}
id=`sbatch --job-name=antsdef${i} --export=ITK_GLOBAL_DEFAULT_NUMBER_OF_THREADS=1,LD_LIBRARY_PATH=$LD_LIBRARY_PATH,ANTSPATH=$ANTSPATH --nodes=1 --cpus-per-task=1 --mem-per-cpu=32768M --time=50:00:00 $QSUBOPTS $qscript | rev | cut -f1 -d\ | rev`
{% endhighlight %}

## Problem 3

The latest version of `N4BiasFieldCorrection` was giving errors and aborting script. I was able to copy the script from the pre-complied code:

{% highlight bash %}
cp -v \
~/apps/ants-2.1.0-redhat/N4BiasFieldCorrection \
~/apps/ants-20151207/bin/N4BiasFieldCorrection
{% endhighlight %}

Here's the end result of using the 20 SOBIK participants. Goosebumps!

![sobiktemplate.jpg]({{site.baseurl}}/media/sobiktemplate.jpg){: .img-responsive }

