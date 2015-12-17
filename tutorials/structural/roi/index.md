---
layout: tutorials
title: ROI Analysis with Desikan Cortical Labeling Protocol
author: naomi
comments: true
date: 2015-12-09
---

Please cite the following article and this website when making use of Mindboggle-101 data:

101 labeled brain images and a consistent human cortical labeling protocol
Arno Klein, Jason Tourville. Frontiers in Brain Imaging Methods. 6:171. DOI: 10.3389/fnins.2012.00171
http://journal.frontiersin.org/article/10.3389/fnins.2012.00171/full

[**CLICK HERE TO DOWNLOAD THE OASIS TEMPLATE IMAGE**](T_template0_BrainCerebellum.nii.gz)

[**CLICK HERE TO DOWNLOAD ALL THE OASIS TEMPLATES FOR antsCorticalThickness.sh**](http://media.mindboggle.info/data/templates/atropos/OASIS-30_Atropos_template.tar.gz)

## Extract ROIs

There are quite a few possible ROIs in the label atlas (see below). If you want to extract a specific ROI, you can use the following Convert 3D code. Use the label image as your input image, threshold the label image the exact label you want. For example, if you want the left hippocampus use label number "17". Convert 3D will take anything labeled "17" and make it a value of "1" and anything **NOT** labeled "17" a value of "0" and output the new ROI image:

{% highlight bash %}
~/apps/c3d/bin/c3d \
OASIS-TRT-20_jointfusion_DKT31_CMA_labels_in_MNI152_v2.nii.gz \
-threshold <labelnumber> <labelnumber> 1 0 \
-o <outputname>.nii.gz
{% endhighlight %}

[**CLICK HERE TO DOWNLOAD THE LABEL IMAGE**](OASIS-TRT-20_jointfusion_DKT31_CMA_labels_in_OASIS-30_v2.nii.gz)


### Cortex Labels and Numbers

* 1002, “left caudal anterior cingulate”
* 1003, “left caudal middle frontal”
* 1005, “left cuneus”
* 1006, “left entorhinal”
* 1007, “left fusiform”
* 1008, “left inferior parietal”
* 1009, “left inferior temporal”
* 1010, “left isthmus cingulate”
* 1011, “left lateral occipital”
* 1012, “left lateral orbitofrontal”
* 1013, “left lingual”
* 1014, “left medial orbitofrontal”
* 1015, “left middle temporal”
* 1016, “left parahippocampal”
* 1017, “left paracentral”
* 1018, “left pars opercularis”
* 1019, “left pars orbitalis”
* 1020, “left pars triangularis”
* 1021, “left pericalcarine”
* 1022, “left postcentral”
* 1023, “left posterior cingulate”
* 1024, “left precentral”
* 1025, “left precuneus”
* 1026, “left rostral anterior cingulate”
* 1027, “left rostral middle frontal”
* 1028, “left superior frontal”
* 1029, “left superior parietal”
* 1030, “left superior temporal”
* 1031, “left supramarginal”
* 1034, “left transverse temporal”
* 1035, “left insula”
* 2002, “right caudal anterior cingulate”
* 2003, “right caudal middle frontal”
* 2005, “right cuneus”
* 2006, “right entorhinal”
* 2007, “right fusiform”
* 2008, “right inferior parietal”
* 2009, “right inferior temporal”
* 2010, “right isthmus cingulate”
* 2011, “right lateral occipital”
* 2012, “right lateral orbitofrontal”
* 2013, “right lingual”
* 2014, “right medial orbitofrontal”
* 2015, “right middle temporal”
* 2016, “right parahippocampal”
* 2017, “right paracentral”
* 2018, “right pars opercularis”
* 2019, “right pars orbitalis”
* 2020, “right pars triangularis”
* 2021, “right pericalcarine”
* 2022, “right postcentral”
* 2023, “right posterior cingulate”
* 2024, “right precentral”
* 2025, “right precuneus”
* 2026, “right rostral anterior cingulate”
* 2027, “right rostral middle frontal”
* 2028, “right superior frontal”
* 2029, “right superior parietal”
* 2030, “right superior temporal”
* 2031, “right supramarginal”
* 2034, “right transverse temporal”
* 2035, “right insula”

### Noncortex Labels and Numbers

* 16, “Brain stem”
* 24, “CSF”
* 14, “3rd ventricle”
* 15, “4th ventricle”
* 72, “5th ventricle”
* 85, “optic chiasm”
* 4, “left lateral ventricle”
* 5, “left inferior lateral ventricle”
* 6, “left cerebellum exterior”
* 7, “left cerebellum white matter”
* 10, “left thalamus proper”
* 11, “left caudate”
* 12, “left putamen”
* 13, “left pallidum”
* 17, “left hippocampus”
* 18, “left amygdala”
* 25, “left lesion”
* 26, “left accumbens area”
* 28, “left ventral DC”
* 30, “left vessel”
* 91, “left basal forebrain”
* 43, “right lateral ventricle”
* 44, “right inferior lateral ventricle”
* 45, “right cerebellum exterior”
* 46, “right cerebellum white matter”
* 49, “right thalamus proper”
* 50, “right caudate”
* 51, “right putamen”
* 52, “right pallidum”
* 53, “right hippocampus”
* 54, “right amygdala”
* 57, “right lesion”
* 58, “right accumbens area”
* 60, “right ventral DC”
* 62, “right vessel”
* 92, “right basal forebrain”
* 630, “cerebellar vermal lobules I-V”
* 631, “cerebellar vermal lobules VI-VII”
* 632, “cerebellar vermal lobules VIII-X”

## Warp ROIs

When you've run `antsRegistrationSyNQuick.sh` you are given a warp and inverse warp image that lets you move between moving and fixed space. For example, let's say you have the thalamus traced in your template image and you want to "trace" the thalamus in each of your participants. It makes sense then to have your template image as the moving image (`-m`) and each participant image is a new fixed image (`-f`). 

When you take the template and normalize to participant:

* Warp matrix represents anything in template space and making it look like the participant
* Inverse warp matrix represents anything in participant space and making it look like the template

Applying the warp matrix to the thalamus ROI (template space) will change the ROI to participant space (the results we want).

The ANTs code to do this is:

{% highlight bash %}
WarpImageMultiTransform \
3 \
<dktROI>.nii.gz \
<output>.nii.gz \
<warpImage>.nii.gz \
<affineFile>.mat \
-R <participantImage>.nii.gz
{% endhighlight %}

<hr>
	
Another example, let's say you have a population of severe TBI participants in which you've already generated ROIs around the major brain lesions and you want to see if there's similarity across participants where lesions occur. It makes sense then to have your template image as your fixed image (`-f`) and each participant image is moving (`-m`).

When you take the participant and normalize to template:

* Warp matrix represents anything in participant space and making it look like the template
* Inverse warp matrix represents anything in template space and making it look like the participant

Applying the warp matrix to the lesion ROI (participant space) will change the ROI to template space (the results we want).

The ANTs code to do this is:

{% highlight bash %}
WarpImageMultiTransform \
3 \
<participantROI>.nii.gz \
<output>.nii.gz \
<warpImage>.nii.gz \
<affineFile>.mat \
-R <templateImage>.nii.gz
{% endhighlight %}

<hr>

You do not have to rerun `antsRegistractionSynQuick.sh` if your fixed and moving images are not precisely correct. You can always apply the inverse warp when needed. Let's go back to the second example where the template image is the fixed image (`-f`) and each participant is a moving image (`-m`). Let's say that you want to move a hippocampus ROI in template space to participant space:

{% highlight bash %}
WarpImageMultiTransform \
3 \
<dktROI>.nii.gz \
<output>.nii.gz \
-i <affineFile>.mat \
<inversewarpImage>.nii.gz \
-R <participantImage>.nii.gz
{% endhighlight %}

### If You Used ANTs Cortical Thickness

If you have already run ANTs Cortical Thickness using the OASIS template the code you need to use to move the ROI in template space to participant space is as follows:

{% highlight bash %}
WarpImageMultiTransform \
3 \
<dktROI>.nii.gz \
<output>.nii.gz \
TemplateToSubject0Warp.nii.gz \
TemplateToSubject1GenericAffine.mat \
-R ExtractedBrain0N4.nii.gz
{% endhighlight %}

You can then clean these ROIs if they are tissue specific. For example, you can clean the hippocampus ROI by multiplying by the gray matter tissue segmentation to remove any extraneous CSF or WM:

{% highlight bash %}
c3d \
BrainSegmentation.nii.gz \
-threshold 2 2 1 0 \
-as MASK \
<participantROI>.nii.gz \
-push MASK \
-multiply \
-o <output>.nii.gz
{% endhighlight %}

## Extract ROI Values

For the example, we will use our tissue segmentation from the ANTs Cortical Thickness pipeline.

### Generate Data

After logging into the supercomputer, we are going to use Convert3D tool to extract the various tissue volumes:

{% highlight bash %}
mkdir -p ~/compute/projects/brainvols/
for subj in $(ls ~/compute/data/); do 
subjDir=~/compute/data/${subj}/
echo $subj >> ~/compute/projects/brainvols/data.csv 
~/apps/c3d/bin/c3d \
${subjDir}/antsCorticalThickness/BrainSegmentation0N4.nii.gz \
${subjDir}/antsCorticalThickness/BrainSegmentation.nii.gz \
-label-statistics >> ~/compute/projects/brainvols/data.csv
done
{% endhighlight %}

### Regular Expressions

![](http://imgs.xkcd.com/comics/regular_expressions.png)

The file is not perfectly formatted so that it can be easily imported into R or even useful in Excel. Some editing of the file needs to happen and we can use the power of Regular Expressions in Terminal to edit the file. Just make sure to change the username in the first line of the code.

{% highlight bash %}
input="/fslhome/username/compute/projects/brainvols/data.csv"
output="${input%.*}_modified.csv"
cd $(dirname $input)
grep -vwE "(Reading #1|LabelID|^(    0))" ${input} | sed -e "s|/|,|" -Ee "s/[ ]+/,/g" > temp.txt
awk '/^[0-9][0-9][0-9][0-9]_[0-9][0-9][0-9][0-9][0-9][0-9]/{for(i=0;i<6;i++)print}' temp.txt > part1.txt
awk '/^,/{print}' temp.txt > part2.txt
sed -i '' -e "s|,1,|,csf,|" -e "s|,2,|,gm,|" -e "s|,3,|,wm,|" -e "s|,4,|,subcortical,|" -e "s|,5,|,brainstem,|" -e "s|,6,|,cerebellum,|" part2.txt 
paste part1.txt part2.txt > ${output}
perl -pi -e 'print "subjectid, label, mean, std, max, min, count, volume (mm^3), extent x, extent y, extent z\n" if($.==1)' ${output}
rm part1.txt
rm part2.txt
rm temp.txt
{% endhighlight %}

Note this code works for the SOBIK dataset. If you have different subject IDs, you'll need to edit the regular expression in line #5. If you need more help figuring out regular expressions visit this website here:

[https://regex101.com/](https://regex101.com/)

Happy Coding!
