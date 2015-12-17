---
title: "Normalization"
author: 
date: "Thursday, October 15, 2015"
output:
  slidy_presentation:
    css: ~/git/byu-f15/stylesheets/mystyle.css
    highlight: haddock
    incremental: yes
    keep_md: yes
---

## Types of Brain Mapping

Moving the properties (whole brain, ROI, overlay, etc.) of a brain onto a template:

- Rigid Body
- Affine
- Diffeomorphic

## Rigid Body

Rigid-body transformations involve displacements and / or rotations of the whole object.

***Volume (size of the brain) is not affected by rigid body transformations.***

## Rigid Body - Move

<img src="images/move.jpg" width="90%" style="float:left" /> 

## Rigid Body - Rotate

<img src="images/rotate.jpg" width="90%" style="float:left" /> 

## Rigid Body - Mirror

<img src="images/mirror.jpg" width="90%" style="float:left" /> 

## Affine

Affine again effects the whole object as a rigid body and adds two addition transformations: scale and shear.

***Affine transformations preserve proportions, it does not preserve volume, because it can change angles and lengths. Therefore, affine transformations automatically account for brain size differences, since affine transformations will make all brains the same size.***

## Affine - Scale

<img src="images/scale.jpg" width="90%" style="float:left" /> 

## Affine - Shear

<img src="images/shear.jpg" width="90%" style="float:left" /> 

## Diffeomorphic

Based on the mathematical field of topology, the simplest definition is that through smooth, continuous deformation, one brain can be warped to look like another brain.

Using diffeomorphic morphometry, this smooth, continuous deformation, preserves the topology of the brain (i.e., sulci and gyri).

## ANTs Nonlinear Registration

For reference, typically the fixed image is a template and the moving image is each participant.

ANTs has two shell scripts for running image registration: antsRegistrationSyn.sh and antsRegistrationSynQuick.sh. Both of these commands are simplified shell scripts of the actual command antsRegistration.

For this class, we will just use `antsRegistrationSynQuick.sh`.

## Comparing Apples and Oranges

<img src="images/comparing.jpg" width="90%" style="float:left" /> 

## Rigid

```
antsRegistrationSynQuick.sh \
-d 3 \
-f <fixedImage>.nii.gz \
-m <movingImage>.nii.gz \
-o <outputPrefix>
-t r
```

----

<img src="images/rigid.jpeg" width="90%" style="float:left" /> 
<img src="images/rigidInverse.jpeg" width="90%" style="float:left" /> 

## Affine

```
antsRegistrationSynQuick.sh \
-d 3 \
-f <fixedImage>.nii.gz \
-m <movingImage>.nii.gz \
-o <outputPrefix>
-t a
```

----

<img src="images/rigid.jpeg" width="90%" style="float:left" /> 
<img src="images/affine.jpeg" width="90%" style="float:left" /> 

----

<img src="images/rigidInverse.jpeg" width="90%" style="float:left" /> 
<img src="images/affineInverse.jpeg" width="90%" style="float:left" /> 

## Diffeomorphic

```
antsRegistrationSynQuick.sh \
-d 3 \
-f <fixedImage>.nii.gz \
-m <movingImage>.nii.gz \
-o <outputPrefix>
-t s
```
----

<img src="images/affine.jpeg" width="90%" style="float:left" /> 
<img src="images/diffeomorphic.jpeg" width="90%" style="float:left" /> 

----

<img src="images/affineInverse.jpeg" width="90%" style="float:left" /> 
<img src="images/diffeomorphicInverse.jpeg" width="90%" style="float:left" /> 

## Warp Fields

The warp matrix contains the directions for where each voxel in the participant image (moving image) needs to move in order to look like the template (fixed image).

ANTs also outputs the inverse warp field. This field contains opposite information; it contains the directions for where each voxel in the template (fixed image) needs to move in order to look like the participant image (moving image).

----

<img src="images/warp.png" width="90%" style="float:left" /> 
