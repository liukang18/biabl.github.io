---
layout: tutorials
title: Group Average Overlays with ANTsR
author: naomi
comments: true
date: 2015-12-09
---

One of the best ways to look at group differences is to create an average of each group's data. For example, if you wanted to look at cortical thickness differences in TBI versus OI, then you would want a group cortical thickness average for the TBI group and another group cortical thickness average of the OI group. Note that when creating group averages, images need to be in template space (i.e., warped to template). 

## Run R

Log into the supercomputer:

{% highlight bash %}
ssh <username>@ssh.fsl.byu.edu
{% endhighlight %}

Load the environmental variables so that R will run on the supercomputer:

{% highlight bash %}
module load r/3.2.1
{% endhighlight %}

Launch R:

{% highlight bash %}
R
{% endhighlight %}

Let's load our packages. 

{% highlight R %}
library(ANTsR)
{% endhighlight %}

Set path to directory containing your data:

{% highlight R %}
setwd("/path/to/data/")
{% endhighlight %}

## Import Data

Depending on how your groups are differentiated, for the SOBIK dataset: 

{% highlight R %}
files=list.files(pattern="13.*")
{% endhighlight %}

Otherwise, you will need to edit the pattern according to [regular expression rules](https://stat.ethz.ch/R-manual/R-devel/library/base/html/list.files.html).

{% highlight R %}
ilist<-list()
for ( i in 1:length(files) )
{
  img<-antsImageRead(files[i])
  img<-smoothImage(img,1.5)
  ilist[i]<-img
}
mask<-getMask(antsImageRead(files[1]))
mat<-imageListToMatrix(ilist, mask)
{% endhighlight %}

## Create Average Image

It's pretty simple to just take an average of each column. Be sure to change the name of your output file according to each group:

{% highlight R %}
aveImage=t(as.matrix(colMeans(mat,na.rm=TRUE,dims=1)))
i.aveImage<-makeImage(mask,aveImage)
antsImageWrite(i.aveImage,file="/path/to/ouput/aveImage.nii.gz")
{% endhighlight %}

## Subtract Groups

For the equation x = a - b:

{% highlight bash %}
c3d a.nii.gz b.nii.gz -scale -1 -add -o x.nii.gz
{% endhighlight %}