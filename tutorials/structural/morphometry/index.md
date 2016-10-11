---
layout: tutorials
title: Types of Morphometry Analyses
author: naomi
comments: true
date: 2015-12-09
---

## Objectives

After you complete this section, you should be able to:

1. Describe tensor based morphometry
2. Know what image is used in TBM
3. Describe voxel based morphometry
4. Know how to generate the image used in VBM

## Tensor Based Morphometry

Tensor based morphometry measures the differences in shape of brain structures. Analyses are useful in studies interested in whether growth or volume loss has occurred. This analysis is also useful in detecting small changes in longitudinal studies.

The warp field only represents positions of brain structures and not local shape information. Instead, the Jacobian determinant contains information about the local stretching, shearing and rotation involved in the deformation. In other words, the warp field provides information about how to move the voxels to template space, whereas the Jacobian determinant represents the extent of movement. Overlaying the Jacobian determinant will show which brain regions has to undergrow significant growth or reducting as compared to the template.

Taking the log of the Jacobian makes it symmetric about zero. **Positive** values indicate tissue **enlargement**, whereas **negative** values indicate tissue **reduction** in the participant as compared to the template.

<img class="img-responsive" alt="" src="images/tbm.jpeg">

Since we have already completed antsCorticalThickness using a study specific template, we can easily generate the log Jacobian from the warp matrix:

{% highlight bash %}


{% endhighlight %}

## Voxel Based Morphometry

Voxel Based Morphometry measures structural differences in tissue classes (e.g., gray matter atrophy). Analyses are useful in studies interested in tissue specific volumetric differences.

The normalized images are adjusted by scaling the intensity of each voxel by the log jacobian. In other words the volume changes due to the non-linear spatial normalization are used to *modulate* the normalized image. Otherwise your analysis will give you null results because all the images look exactly alike (that's the point of normalizing to a template).

<img class="img-responsive" alt="" src="images/workflow.png">

### Group Analysis

The modulated normalized gray matter image is used for analyses. This image has to be self generated.

## Modulate Gray Matter Image

First, you will take the template GM ROI and multiply it with the normalization whole brain image. Remember the value inside the ROI is 1 and the value outside the ROI is 0. When you multiply anything by 0 it equals 0, so when you multiply these two images together, you are left with just normalized gray matter.

<div class="row">
    <div class="col-xs-6">
		<img class="img-responsive" alt="" src="images/brain.png">
	</div>
	<div class="col-xs-6">
		<img class="img-responsive" alt="" src="images/roi.png">
	</div>
</div>

{% highlight bash %}
SUBJ_DIR=~/path/to/subject/directory
TEMPLATE_DIR=~/path/to/template/directory/
c3d \
${SUBJ_DIR}/corticalthickness/BrainNormalizedToTemplate.nii.gz \
${TEMPLATE_DIR}/T_template0_gm.nii.gz \
-multiply \
-o ${SUBJ_DIR}/corticalthickness/GMSegmented.nii.gz
{% endhighlight %}

<img class="img-responsive" alt="" src="images/segmented.png">

The image has been normalized to a template, so at this point the image should be nearly indistinguishable from the template. Any comparison will result in null results. However, if you multiply the image by the log Jacobian, then you are adjusting the intensity of each gray matter voxel by the amount of stretching, shearing, and rotation that occurred.

{% highlight bash %}
c3d \
${SUBJ_DIR}/corticalthickness/GMSegmented.nii.gz \
${SUBJ_DIR}/corticalthickness/SubjectToTemplateLogJacobian.nii.gz \
-multiply \
-o ${SUBJ_DIR}/corticalthickness/GMModulated.nii.gz
{% endhighlight %}

<img class="img-responsive" alt="" src="images/modulated.png">

### Additional Classroom Materials

* [Lecture](presentation)
* [Assignment](assignment)
