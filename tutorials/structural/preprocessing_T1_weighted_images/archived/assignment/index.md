---
title: "Preprocessing T1 Images"
author: "Assignment"
date: "Monday, October 26, 2015"
output:
  slidy_presentation:
    css: ~/git/byu-f15/stylesheets/mystyle.css
    highlight: haddock
    incremental: yes
    keep_md: yes
---

## Let's get started!

Log into supercomputer:

```
ssh <username>@ssh.fsl.byu.edu
```

Move files:

```
rsync \
-rauv \
~/fsl_groups/fslg_byustudent/compute/data \
~/compute/
```

## Convert DICOMs to NIfTI

Set participant directory:

```
subjDir=~/compute/data/1304_082907/
```

Make a new directory for your NIfTI images:

```
mkdir ${subjDir}/t1
```

## dcm2nii

The program `dcm2nii` will anonymize, reorient and crop images:

```
~/apps/mricron/dcm2nii \
-a y \
-g n \
-x y \
-o ${subjDir}/t1 \
${subjDir}/DICOMs/mprage/*
```

## Rename

The output from the `dcm2nii` program results in file names that are uniquely different across participants. For analyses though, we want all the files named exactly the same from participant to participant. In this case, we want all the files named t1.nii.

In addition to renaming the file, we want to use just the cropped and reorientated file (co). We want to delete the reorientation only (o) and original file (no co or o prefix).

```
cd ${subjDir}/t1
mv co*.nii t1.nii
rm o*.nii | rm 2*.nii
```

## acpcdetect

The program `acpcdetect` will AC-PC align images, but the program only runs on Linux:

```
~/apps/art/acpcdetect \
-M \
-o ${subjDir}/t1/acpc.nii \
-i ${subjDir}/t1/t1.nii
```

## N4 Bias Field Correction

The way to fix the bias field is to use ANTs N4 Bias Field Correction tool:

```
~/apps/ants/bin/N4BiasFieldCorrection \
-d 3 \
-i ${subjDir}/t1/acpc.nii \
-o [${subjDir}/t1/n4.nii.gz,${subjDir}/t1/biasfield.nii.gz] \
-s 4 \
-b [200] \
-c [50x50x50x50,0.000001]
```

## Resample to 1 mm Isotropic

```
~/apps/c3d/bin/c3d \
${subjDir}/t1/n4.nii.gz \
-resample-mm 1x1x1mm \
-o ${subjDir}/t1/resampled1iso.nii.gz
```

Never perfect though!

```
~/apps/c3d/bin/c3d \
${subjDir}/t1/resampled1iso.nii.gz \
-info-full
```

## Copy to Desktop

Exit from the secure shell or just open a new shell window:

```
exit
```

Sync leaving out the DICOM folders:

```
rsync \
-rauv \
--exclude="DICOMs" \
<username>@ssh.fsl.byu.edu:~/data/1304_082907 \
~/Desktop
```

### Homework

Before class on Monday, November 2nd, 2015, process all the participants.


