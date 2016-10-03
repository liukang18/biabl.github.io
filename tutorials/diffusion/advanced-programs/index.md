---
layout: tutorials
title: Install Programs
author: naomi
comments: true
date: 2016-08-22
---

## Objectives

After you complete this section, you should be able to:

1. Install FSL and update shell environment
2. Load MATLAB
3. Use git to install VistaSoft and AFQ
4. Download and unzip SPM5 and SPM8

**Before you install the following programs, make sure you have already installed the programs listed under the beginner tutorials: [http://biabl.github.io/tutorials/general/introduction-to-fulton-supercomputer-lab](http://biabl.github.io/tutorials/general/introduction-to-fulton-supercomputer-lab)**

## FSL

<div class="embed-container">
<iframe src="https://player.vimeo.com/video/179801914?byline=0&portrait=0" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>
</div>
<br>

After following the instructions in the above video, you need to update your .bash_profile:

{% highlight bash %}
vi ~/.bash_profile
{% endhighlight %}

Add the following to the end of your .bash_profile:

{% highlight bash %}
# FSL
FSLDIR=/fslhome/intj5/apps/fsl/
. ${FSLDIR}/etc/fslconf/fsl.sh
PATH=${FSLDIR}/bin:${PATH}
export FSLDIR PATH
{% endhighlight %}

## MATLAB

<div class="embed-container">
<iframe src="https://player.vimeo.com/video/179801915?byline=0&portrait=0" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>
</div>

## VistaSoft

<div class="embed-container">
<iframe src="https://player.vimeo.com/video/179801916?byline=0&portrait=0" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>
</div>

## Statistical Parametric Mapping (SPM)

No need to provide a video...installation is straight-forward. Download the zip files and unzip:

{% highlight bash %}
cd ~/apps/matlab/
wget http://www.fil.ion.ucl.ac.uk/spm/download/restricted/contradistinction/spm5.zip
unzip spm5.zip && rm spm5.zip
wget http://www.fil.ion.ucl.ac.uk/spm/download/restricted/idyll/spm8.zip
unzip spm8.zip && rm spm8.zip
{% endhighlight %}
