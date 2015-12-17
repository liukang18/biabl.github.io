---
title: "ANTs Cortical Thickness"
author: "Assignment"
date: "Monday, November 2, 2015"
output:
  slidy_presentation:
    css: ~/git/byu-f15/stylesheets/mystyle.css
    highlight: haddock
    incremental: yes
    keep_md: yes
---

## Copy Example to your Desktop:

```
rsync \
-rauv \
<username>@ssh.fsl.byu.edu:~/fsl_groups/fslg_byustudent/compute/examples/corticalthickness \
~/Desktop
```

----

T1.nii.gz with BrainExtractionMask.nii.gz as ROI

<img src="images/BrainExtractionMask.png" width="60% style="float:center" />

----

T1.nii.gz with BrainSegmentation.nii.gz as ROI

<img src="images/BrainSegmentation.png" width="60% style="float:center" />

----

T1.nii.gz with Posteriors1.nii.gz as Overlay [0.25,1]

<img src="images/Posteriors1.png" width="60% style="float:center" />

----

T1.nii.gz with Posteriors2.nii.gz as Overlay [0.25,1]

<img src="images/Posteriors2.png" width="60% style="float:center" />

----

T1.nii.gz with Posteriors3.nii.gz as Overlay [0.25,1]

<img src="images/Posteriors3.png" width="60% style="float:center" />

----

T1.nii.gz with Posteriors4.nii.gz as Overlay [0.25,1]

<img src="images/Posteriors4.png" width="60% style="float:center" />

----

T1.nii.gz with Posteriors5.nii.gz as Overlay [0.25,1]

<img src="images/Posteriors5.png" width="60% style="float:center" />

----

T1.nii.gz with Posteriors6.nii.gz as Overlay [0.25,1]

<img src="images/Posteriors6.png" width="60% style="float:center" />

----

T1.nii.gz with CorticalThickness.nii.gz as Overlay [1,9]

<img src="images/CorticalThickness.png" width="60% style="float:center" />

----

Extracted Brain

<img src="images/ExtractedBrain.png" width="60% style="float:center" />

----

BrainNormalizedToTemplate.nii.gz with CorticalThicknessNormalizedToTemplate.nii.gz as Overlay [1,9]

<img src="images/CorticalThicknessNormalizedToTemplate.png" width="60% style="float:center" />

----

BrainNormalizedToTemplate.nii.gz with SubjectToTemplateLogJacobian.nii.gz as Overlay [-0.5,0.5]

<img src="images/SubjectToTemplateLogJacobian.png" width="60% style="float:center" />

----

Subject to template inverse (AKA template normalized to subject)

<img src="images/SubjectToTemplateInverseWarped.png" width="60% style="float:center" />