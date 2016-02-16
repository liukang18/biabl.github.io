---
layout: post
excerpt_separator: "<!--more-->"
title: Optimizing ANTs Joint Label Fusion
tags: 
  - ANTs
  - bug
  - "tips 'n tricks"
  - antsJointLabelFusion.sh
author: naomihunsaker
published: true
---

Ran into a problem or two, but mostly it was about optimizing the pipeline.

<!-- more -->

Here's the final code I used to run the SOBIK template:

{% highlight bash %}
TEMPLATE_DIR=/fslhome/intj5/templates/OASIS-TRT-20_volumes
LABEL_DIR=/fslhome/intj5/templates/OASIS-TRT-20_DKT31_CMA_labels_v2
cd /fslhome/intj5/compute/templates/SOBIK
mkdir labels
${ANTSPATH}/antsJointLabelFusion.sh \
-d 3 \
-c 5 -j 2 -y s -k 1 \
-o /fslhome/intj5/compute/templates/SOBIK/labels/ \
-p /fslhome/intj5/compute/templates/SOBIK/labels/Posteriors%02d.nii.gz \
-t /fslhome/intj5/compute/templates/SOBIK/data/sobikbraintemplate.nii.gz \
-g ${TEMPLATE_DIR}/OASIS-TRT-20-1/t1weighted_brain.nii.gz -l ${LABEL_DIR}/OASIS-TRT-20-1_DKT31_CMA_labels.nii.gz \
-g ${TEMPLATE_DIR}/OASIS-TRT-20-2/t1weighted_brain.nii.gz -l ${LABEL_DIR}/OASIS-TRT-20-2_DKT31_CMA_labels.nii.gz \
-g ${TEMPLATE_DIR}/OASIS-TRT-20-3/t1weighted_brain.nii.gz -l ${LABEL_DIR}/OASIS-TRT-20-3_DKT31_CMA_labels.nii.gz \
-g ${TEMPLATE_DIR}/OASIS-TRT-20-4/t1weighted_brain.nii.gz -l ${LABEL_DIR}/OASIS-TRT-20-4_DKT31_CMA_labels.nii.gz \
-g ${TEMPLATE_DIR}/OASIS-TRT-20-5/t1weighted_brain.nii.gz -l ${LABEL_DIR}/OASIS-TRT-20-5_DKT31_CMA_labels.nii.gz \
-g ${TEMPLATE_DIR}/OASIS-TRT-20-6/t1weighted_brain.nii.gz -l ${LABEL_DIR}/OASIS-TRT-20-6_DKT31_CMA_labels.nii.gz
{% endhighlight %}

## Problem 1

My code wouldn't run if I didn't specifically state my transformation type is `-y s`, even though that's the default. Make sure to add that option to your code.

## Problem 2

The generated `job_t1weighted_0.sh` file contained the wrong code:

{% highlight bash %}
#!/bin/sh
/fslhome/intj5/apps/ants-20151207/bin//antsRegistrationSyNQuick.sh -d 3 -p d -j 1 -t s -f /fslhome/intj5/compute/templates/SOBIK/data/sobiktemplate.nii.gz -m /fslhome/intj5/templates/OASIS-TRT-20_volumes/OASIS-TRT-20-1/t1weighted.nii.gz -o /fslhome/intj5/compute/templates/SOBIK/labels/t1weighted_0_ > /fslhome/intj5/compute/templates/SOBIK/labels/t1weighted_0_log.txt
/fslhome/intj5/apps/ants-20151207/bin//antsApplyTransforms -d 3 --float 1                           -i /fslhome/intj5/templates/OASIS-TRT-20_volumes/OASIS-TRT-20-1/labels.DKT25.manual.nii.gz -r /fslhome/intj5/compute/templates/SOBIK/data/sobiktemplate.nii.gz -o /fslhome/intj5/compute/templates/SOBIK/labels/t1weighted_0_WarpedLabels.nii.gz -n NearestNeighbor                           -t /fslhome/intj5/compute/templates/SOBIK/labels/t1weighted_0_0Warp.nii.gz >> /fslhome/intj5/compute/templates/SOBIK/labels/t1weighted_0_log.txt
{% endhighlight %}

The error is that when you run antsRegistrationSynQuick.sh, the output warp file should be *t1weighted_0_1Warp.nii.gz* and not *t1weighted_0_0Warp.nii.gz*, so when it's trying to run antsApplyTransforms, it can't find the files. Moreover the affine.mat file is also required. 

I simply commented out the offending lines (532 - 542) in the antsJointLabelFusion.sh script:

{% highlight bash %}
#     if [[ ! -f "${OUTPUT_PREFIX}${BASENAME}_${i}_0GenericAffine.mat" ]];
#       then
#         labelXfrmCall="${ANTSPATH}/antsApplyTransforms \
#                               -d ${DIM} \
#                               --float 1 \
#                               -i ${ATLAS_LABELS[$i]} \
#                               -r ${TARGET_IMAGE} \
#                               -o ${OUTPUT_PREFIX}${BASENAME}_${i}_WarpedLabels.nii.gz \
#                               -n NearestNeighbor \
#                               -t ${OUTPUT_PREFIX}${BASENAME}_${i}_0Warp.nii.gz >> ${OUTPUT_PREFIX}${BASENAME}_${i}_log.txt"
#       fi
{% endhighlight %}

## Problem 3

Throughout the antsJointLabelFusion.sh code, the slurm memory limit is set to 8GB, which is too low. The supercomputer will not complete the code with so few resources. I went through the script and changed everything to `--mem-per-cpu=32768M`. So far no problems completing the code with the memory set this high.

## Problem 4

I was originally trying to run my template which was 1x0.5x0.5 voxels. This was NOT going to run the final step. Make sure your target image is at least 1 isotropic.

## Problem 5 & 6

Brain extracted image versus whole brain. I first ran with a target image that was not skull stripped. The first step of registering each labeled image to the target image resulted in bad registration. I tried to solve this problem by adding the option `-x` to include a target mask image. The initial registration was STILL awful, and instead of labeling the brain included in the mask, JLF only labeled the sparse few brain voxels that snuck outside the mask?!

The final solution was to make sure the target and label images were all skull stripped and no target mask image was included.

![sagittal0050.png]({{site.baseurl}}/media/sagittal0050.png){: .img-responsive }
![sagittal0060.png]({{site.baseurl}}/media/sagittal0060.png){: .img-responsive }
![sagittal0070.png]({{site.baseurl}}/media/sagittal0070.png){: .img-responsive }
![sagittal0080.png]({{site.baseurl}}/media/sagittal0080.png){: .img-responsive }
![sagittal0090.png]({{site.baseurl}}/media/sagittal0090.png){: .img-responsive }
![sagittal0100.png]({{site.baseurl}}/media/sagittal0100.png){: .img-responsive }
![sagittal0110.png]({{site.baseurl}}/media/sagittal0110.png){: .img-responsive }
![sagittal0120.png]({{site.baseurl}}/media/sagittal0120.png){: .img-responsive }
![sagittal0130.png]({{site.baseurl}}/media/sagittal0130.png){: .img-responsive }
![sagittal0140.png]({{site.baseurl}}/media/sagittal0140.png){: .img-responsive }
![sagittal0150.png]({{site.baseurl}}/media/sagittal0150.png){: .img-responsive }
![sagittal0160.png]({{site.baseurl}}/media/sagittal0160.png){: .img-responsive }
![sagittal0170.png]({{site.baseurl}}/media/sagittal0170.png){: .img-responsive }
![sagittal0180.png]({{site.baseurl}}/media/sagittal0180.png){: .img-responsive }
![sagittal0190.png]({{site.baseurl}}/media/sagittal0190.png){: .img-responsive }
![sagittal0200.png]({{site.baseurl}}/media/sagittal0200.png){: .img-responsive }
