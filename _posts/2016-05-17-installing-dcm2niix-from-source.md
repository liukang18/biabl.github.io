---
excerpt_separator: "<!-- more -->"
layout: post
published: true
title: Installing dcm2niix from source
author: chriscutler
tags: 
  - bug
  - "tips "
  - DICOM
  - Convert
  - dcm2nii
  - NIFTI
  - nii
---
Finding an error in dcm2nii led me to find a newer version dcm2niix that is constantly maintained:
<!-- more -->

## dcm2nii Error

After coming across an error with dcm2nii I emailed Chris Rorden, the creator of dcm2nii, to ask if he had seen the same error before. dcm2nii was distorting the original DICOMs when it converted them NIFTI. Here is a before:

![Screen Shot 2016-05-17 at 13.40.31.png]({{site.baseurl}}/media/Screen Shot 2016-05-17 at 13.40.31.png)

And after running dcm2nii:

![Screen Shot 2016-05-17 at 13.41.44.png]({{site.baseurl}}/media/Screen Shot 2016-05-17 at 13.41.44.png)

This only occurred with some subjects in my dataset but was common enough to make me wonder what was causing it. Opening the DICOMs in OsiriX confirmed that the original scan was not distorted in any way.

## Installing the New Version

Chris informed me of a new version of dcm2nii called dcm2niix. The github repo can be found at: [GitHub](https://github.com/neurolabusc/dcm2niix) and can be installed by following these steps.

1. log into your supercomputer account
2. cd into the folder where you want dcm2niix to build
3.

     git clone https://github.com/neurolabusc/dcm2niix.git
     mkdir dcm
     cd dcm
     make ../dcm2niix

4. in ccmake hit 'c' then 'g'
5. exit back into the terminal and type
      
     make -j 4
     
You should now have a complied version of dcm2niix.
