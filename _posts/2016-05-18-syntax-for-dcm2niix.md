---
excerpt_separator: "<!-- more -->"
layout: post
published: false
title: Syntax for dcm2niix
author: chriscutler
tags: 
  - ANTs
  - "tips "
  - preprocessing
  - dicoms
  - dcm2nii
  - dcm2niix
  - convert
---
dcm2niix is a lot faster and more streamlined than dcm2niix.
<!-- more -->
## Converting DICOM to NIFTI

dcm2niix simplifies the ammount of code required to convert DICOMs. The following is a bash loop that will convert all of your dicoms.
    for subj in $(ls <path to subjects>); do
    echo $subj 
    subjDir=<path to subjects>/$subj
    /fslhome/ccutle25/bin/dcm2niix/bin/dcm2niix \
    -f t1 \
    $subjDir/
    done
    
dcm2niix only requires the file path to your subject to run. This is an improvement over dcm2nii becuase dcm2niix will recursively search a folder for any DICOMs and place the converted image in the folder you specified.

For example:
This is greatly advantageous when you have a subject (subj1) that contains a a randomly named subfolder (000000fhf) that contains your DICOMs. Instead of passing /fslhome/compute/data/subj1/0000000fhf/ as the location of your DICOMs you can simply pass it /fslhome/compute/data/subj1/ and it will do the rest. No more renaming of subfolders so your for loop will run properly.

dcm2niix is also FAST! It will convert a image in about .5 seconds which greatly helps on large datasets. 

-f is the name you want for the converted image. Unless you specify other flags within the -f flag, all patient data is removed.

Additional information can be found at <http://www.nitrc.org/plugins/mwiki/index.php/dcm2nii:MainPage>

or by typing in the path to dcm2niix without an arguments.

