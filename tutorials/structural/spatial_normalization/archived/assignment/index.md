---
title: "Normalization"
author: "Assignment"
date: "Monday, October 19, 2015"
output:
  slidy_presentation:
    css: ~/git/byu-f15/stylesheets/mystyle.css
    highlight: haddock
    incremental: yes
    keep_md: yes
---

Make a new directory for example projects:

```
mkdir ~/compute/examples
```

Sync the new example from the byustudent group. The new example is called, morphometry:

```
rsync \
-rauv \
~/fsl_groups/fslg_byustudent/compute/examples/morphometry \
~/compute/examples
```

Change to that new directory:

```
cd ~/compute/examples/morphometry
```

----

## Rigid

You always want to list full path names when referencing files. It is easiest if you create a variable with your path directory so you just reference that variable and not type your full path over and over again.

```
pathDir=~/compute/examples/morphometry/
```

Rigidly align the t1 image (moving) to the template (fixed).

```
~/apps/ants/bin/antsRegistrationSyNQuick.sh \
-d 3 \
-f ${pathDir}/template.nii \
-m ${pathDir}/t1.nii.gz \
-o ${pathDir}/rigid_ \
-t r
```

The rigid alignment should take <5 minutes.

## What you should end up with

```
rigid_0GenericAffine.mat
rigid_InverseWarped.nii.gz
rigid_Warped.nii.gz
```

## Affine

```
~/apps/ants/bin/antsRegistrationSyNQuick.sh \
-d 3 \
-f ${pathDir}/template.nii \
-m ${pathDir}/t1.nii.gz \
-o ${pathDir}/affine_ \
-t a
```

Affine alignment should take ~10 minutes.  

## What you should end up with

```
affine_0GenericAffine.mat
affine_InverseWarped.nii.gz
affine_Warped.nii.gz
```

## Diffeomorphic

```
~/apps/ants/bin/antsRegistrationSyNQuick.sh \
-d 3 \
-f ${pathDir}/template.nii \
-m ${pathDir}/t1.nii.gz \
-o ${pathDir}/syn_ \
-t s
```

Diffeomorphic registration should take ~30 minutes.

## What you should end up with

```
syn_0GenericAffine.mat
syn_1InverseWarp.nii.gz
syn_1Warp.nii.gz
syn_InverseWarped.nii.gz
syn_Warped.nii.gz
```

## Copy files to your Desktop

Exit from your secure shell (you can also just open a new terminal window):

```
exit
```

Copy the files to your Desktop and don't forget to change your username:

```
rsync \
-rauv \
<username>@ssh.fsl.byu.edu:~/compute/examples/morphometry \
~/Desktop
```

## Before leaving class if you could...

Log into supercomputer:

```
ssh <username>@ssh.fsl.byu.edu
```

Copy over data:

```
rsync \
-rauv \
~/fsl_groups/fslg_byustudent/compute/data \
~/compute/
```
