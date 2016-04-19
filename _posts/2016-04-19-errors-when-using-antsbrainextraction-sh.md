---
excerpt_separator: "<!-- more -->"
layout: post
author: chriscutler
tags: 
  - ANTs
  - bug
  - "tips "
  - Skull Strip
  - Brain Extraction
  - ANTs version
published: true
title: Errors When Using antsBrainExtraction.sh
---
Using antsBrainExtraction can be a pain. Here are some common problems you might run into and how to fix them. 

<!-- more -->

# ITK Warnings

If you are ever trying to skull strip a participant using antsBrainExtraction.sh and you get an error like this somewhere in your terminal output or logfile:

    BEGIN >>>>>>>>>>>>>>>>>>>>
	/fslhome/ccutle25/bin/antsbin/bin/antsAI -d 3 -v 1 -m MI[/fslhome/ccutle25/compute/ucsd_t1/sd_001--2012-09-
    20/BrainExtractionInitialAffineFixed.nii.gz,/fslhome/ccutle25/compute/ucsd_t1/sd_001--2012-09-
    20/BrainExtractionInitialAffineMoving.nii.gz,32,Regular,0.25] -t Affine[0.1] -s [15,0.1] -p 0 -c 10 -o
    /fslhome/ccutle25/compute/ucsd_t1/sd_001--2012-09-20/BrainExtractionInitialAffine.mat
    
	Using the Mattes MI metric (number of bins = 32)
	WARNING: In /fslhome/ccutle25/bin/antsbin/ITKv4-install/include/ITK-4.9/itkObjectToObjectMetric.hxx, line 529
	JointHistogramMutualInformationImageToImageMetricv4 (0x36ab810): No valid points were found during metric
    evaluation. For image metrics, verify that the images overlap appropriately. For instance, you can align the
    image centers by translation. For point-set metrics, verify that the fixed points, once transformed into the
    virtual domain space, actually lie within the virtual domain.

	*** Running AffineTransform registration ***

	Exception caught: 
	itk::ExceptionObject (0x3983be0)
	Location: "unknown" 
	File: /fslhome/ccutle25/bin/antsbin/ITKv4-install/include/ITK-
    4.9/itkMattesMutualInformationImageToImageMetricv4.hxx
	Line: 238
	Description: itk::ERROR: MattesMutualInformationImageToImageMetricv4(0x39124b0): Too many samples map outside
    moving image buffer. There are only 397 valid points out of 7686 total points. The images do not sufficiently
    overlap. They need to be initialized to have more overlap before this metric will work. For instance, you can
    align the image centers by translation.
    
This is caused by using a template that does not represent your dataset. Usually this means you are using a skull stripped template when you have subjects that are not skull stripped (yet).

Along with this error you will get a skull stripped brain that looks like this:

![e7361db6-053a-11e6-98a8-47b34d0be85b.png]({{site.baseurl}}/media/e7361db6-053a-11e6-98a8-47b34d0be85b.png)

Horrible right?

To fix this you can add this code to antsBrainExtraction.sh:

	-f /fslhome/ccutle25/templates/OASIS/OASIS-30_Atropos_template/T_template0_BrainCerebellumRegistrationMask.nii.gz \

This flag will limit ANTs to running metric computation to a specific area in the scan. If your template doesn't include the above mask, try creating a template from your dataset or use another template that is not skull stripped.

# Seg Fault Error

For awhile I also encountered this error that appeared even after updating ANTs to the most current version and debugging my code.

	/fslhome/ccutle25/bin/ANTs/Scripts/antsBrainExtraction.sh: line 105: 101457 Killed                  $cmd
	ERROR: command exited with nonzero status 137

This problem can be mitigated by compiling ANTs using gcc 4.4.8. First uninstall ANTs from your account.

Then unistall the current gcc module in your supercomputer account using:

	module unload compiler_gnu/4.9.2
    
And then load the version we want. This will be a temporary change that will only last as long as you are logged into your account.

    module load compiler_gnu/4.4

Now you can follow the instructions found here: [GitHub](https://brianavants.wordpress.com/2012/04/13/updated-ants-compile-instructions-april-12-2012/)

# Other Tips for antsBrainExtraction.sh

Because the script runs N4 Bias correction, you do not need to run N4BiasFieldCorrection on your subjects before running antsBrainExtraction.

If all else fails, use BET.
    







Enter text in [Markdown](http://daringfireball.net/projects/markdown/). Use the toolbar above, or click the **?** button for formatting help.
