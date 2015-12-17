---
layout: tutorials
title: Other Ways to Convert to NIfTI
author: naomi
comments: true
date: 2015-12-09
---

Sometimes you are not always given MR images in raw DICOM format. In these instances, you still want to convert to NIFTI image. Here are some examples of how to convert to NIfTI from some of the other types of imaging formats:

## Extract tar.gz File

{% highlight bash %}
tar -zxvf file.tar.gz
{% endhighlight %}

## Analyze IMG / HDR Format

You will probably have to make some additional changes if you are using the OASIS dataset, which gives you totally wrong orientation of the images and files saved in a format that cannot be read by *acpcdetect*.

If you *JUST* want to convert IMG / HDR to NIfTI:

{% highlight bash %}
c3d <input>.hdr -o <output>.nii
{% endhighlight %}

* Add the option `-type ushort` to format for acpcdetect.
* Add the option `-orient PIR` to reorient images not in standard orientation.
* Add the option `-trim 5vox` to crop image with excess background.

So for example if you were to include all the options:

{% highlight bash %}
c3d \
<input>.hdr \
-type ushort \
-orient PIR \
-trim 5vox \
-o <output>.nii
{% endhighlight %}

## FreeSurfer MGZ Format

Assuming you have  FreeSurfer installed on your computer:

{% highlight bash %}
mri_convert /path/to/<input>.mgz /path/to/<output>.nii
{% endhighlight %}

## DICOM But Not DICOM

Sometimes you receive DICOM images, but they do not have the .dcm after their file name. You will need to add that to ALL the DICOM files. With mac you can use an application like [Better Rename](https://itunes.apple.com/us/app/better-rename-9/id414209656?mt=12). It can also be done in a for loop through terminal.