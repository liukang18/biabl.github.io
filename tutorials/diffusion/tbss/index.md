---
layout: tutorials
title: Tract-Based Spatial Statistics
author: naomi
comments: true
date: 2016-08-02
---

## Objectives

After you complete this section, you should be able to:

1. Describe TBSS and it's strengths and weaknesses
2. Eddy-current correct, skull strip, and fit tensors to the whole brain
3. Run and describe the 4 steps of the TBSS analysis
4. Run TBSS on non-FA images

## Tract Based Spatial Statistics (TBSS)

Diffusion weighted imaging (DWI) measures the direction of water diffusion in brain tissue and is thought to be an indicator of the fiber tract integrity: reflecting coherence, organization and/or density of the fiber bundles. The most common standard diffusion measure is fractional anisotropy (FA). The value of FA, which ranges from 0 to 1, is highest in major white matter tracts (FA value approaches 1), lower in gray matter, and approaches 0 in cerebrospinal fluid. FA is thought to be a marker of white matter integrity, because variations reflect myelination, axon density, axonal membrane integrity, and axon diameter. Axial diffusivity (AD) represents diffusivity along the principal eigenvector. In contrast, radial diffusivity (RD) describes an average of the eigenvectors perpendicular to the principal direction.

TBSS is an automated method that tries to combine the strengths of both voxel-based morphometry (VBM) and tract-based spatial statistic methods. In VBM analyses, each participant’s FA image is registered to a template and then voxelwise statistics are computed to find brain areas that differ between groups. One major limitation to VBM methods is that coregistration algorithms are not able to accurately align fiber tracts across participants. This has been a long-standing issue with any VBM analysis. There is no way to ensure that a voxel in standard space contains data from the same portion of the brain across participants. Long projection white matter fiber tracts vary too much in size and shape and there is no way to be sure that registration of every participant’s FA image to a common space has been completely successful. TBSS tries to overcome these limitations of VBM, but TBSS still relies on registering participants to a common template space and only provides a modest improvement over traditional VBM methods. TBSS techniques also cannot ensure that any voxel corresponds to the same tract across participants. Notwithstanding these limitations, TBSS analyses remain the most common approach to analyzing diffusion weighted images.

## Preprocessing DWI

<div class="embed-container">
<iframe src="https://player.vimeo.com/video/183678370" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>
</div>

Each diffusion-weighted image is registered to the non-diffusion-weighted (b=0) using linear image registration for motion correction using FSL eddy_correct program. After correction, diffusion weighted images are skull-stripped in order to exclude non-brain voxels from all analyses using FSL bet program. Last, the diffusion tensors are calculated with the FSL DTIfit program for whole brain volumes and resulting FA, RD, MD, and AD volumes can then be used in tract-based spatial statistics analysis.

### Job Script

Create script file:

{% highlight bash %}
vi ~/scripts/EDSD/fdt_job.sh
{% endhighlight %}

Copy and paste into the script file:

{% highlight bash %}
#!/bin/bash

#SBATCH --time=02:00:00   # walltime
#SBATCH --ntasks=1   # number of processor cores (i.e. tasks)
#SBATCH --nodes=1   # number of nodes
#SBATCH --mem-per-cpu=16384M  # memory per CPU core

# Compatibility variables for PBS. Delete if not needed.
export PBS_NODEFILE=`/fslapps/fslutils/generate_pbs_nodefile`
export PBS_JOBID=$SLURM_JOB_ID
export PBS_O_WORKDIR="$SLURM_SUBMIT_DIR"
export PBS_QUEUE=batch

# Set the max number of threads to use for programs using OpenMP.
export OMP_NUM_THREADS=$SLURM_CPUS_ON_NODE

# LOAD ENVIRONMENTAL VARIABLES
var=`id -un`
FSLDIR=/fslhome/$var/apps/fsl
PATH=${FSLDIR}/bin:${PATH}
export FSLDIR PATH
. ${FSLDIR}/etc/fslconf/fsl.sh

# INSERT CODE, AND RUN YOUR PROGRAMS HERE
cd ~/compute/images/EDSD/${1}/raw/
eddy_correct dti.nii.gz data.nii.gz 0
bet data data_brain -f 0.25 -g 0 -m
dtifit --data=data.nii.gz --out=dti --mask=data_brain_mask.nii.gz --bvecs=dti.bvec --bvals=dti.bval --save_tensor
{% endhighlight %}

### Batch Script

Create batch script file:

{% highlight bash %}
vi ~/scripts/EDSD/fdt_batch.sh
{% endhighlight %}

Copy and paste into the batch script file:

{% highlight bash %}
#!/bin/bash

for subj in $(ls ~/compute/images/EDSD/); do
sbatch \
-o ~/logfiles/${1}/output_${subj}.txt \
-e ~/logfiles/${1}/error_${subj}.txt \
~/scripts/EDSD/fdt_job.sh \
${subj}
sleep 1
done
{% endhighlight %}

### Submit Batch Script

Use the following code to submit the batch script:

{% highlight bash %}
var=`date +"%Y%m%d-%H%M%S"`
mkdir -p ~/logfiles/$var
sh ~/scripts/EDSD/fdt_batch.sh $var
{% endhighlight %}

## Running TBSS

### tbss_1_preproc

<div class="embed-container">
<iframe src="https://player.vimeo.com/video/183678374" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>
</div>

Prepare your FA data in your TBSS working directory. You will need to copy all the FA images into a single directory:

{% highlight bash %}
mkdir -p ~/compute/analyses/EDSD/TBSS/
for i in $(ls ~/compute/images/EDSD/); do
cp -v ~/compute/images/EDSD/${i}/raw/dti_FA.nii.gz ~/compute/analyses/EDSD/TBSS/${i}_FA.nii.gz
done
{% endhighlight %}

You are now nearly ready to run the first TBSS script, which will erode your FA images slightly and zero the end slices (to remove likely outliers from the diffusion tensor fitting). This step skeletonizes all participants’ FA volumes.

Create a job script:

{% highlight bash %}
vi ~/scripts/EDSD/tbss_1_preproc.sh
{% endhighlight %}

Copy and paste the following into the new script:

{% highlight vim %}
#!/bin/bash

#SBATCH --time=00:05:00   # walltime
#SBATCH --ntasks=1   # number of processor cores (i.e. tasks)
#SBATCH --nodes=1   # number of nodes
#SBATCH --mem-per-cpu=24576M  # memory per CPU core

# Compatibility variables for PBS. Delete if not needed.
export PBS_NODEFILE=`/fslapps/fslutils/generate_pbs_nodefile`
export PBS_JOBID=$SLURM_JOB_ID
export PBS_O_WORKDIR="$SLURM_SUBMIT_DIR"
export PBS_QUEUE=batch

# Set the max number of threads to use for programs using OpenMP.
export OMP_NUM_THREADS=$SLURM_CPUS_ON_NODE

# LOAD ENVIRONMENTAL VARIABLES
var=`id -un`
FSLDIR=/fslhome/$var/apps/fsl
PATH=${FSLDIR}/bin:${PATH}
export FSLDIR PATH
. ${FSLDIR}/etc/fslconf/fsl.sh

# INSERT CODE, AND RUN YOUR PROGRAMS HERE
cd ~/compute/analyses/EDSD/TBSS/
tbss_1_preproc \*.nii.gz
{% endhighlight %}

We don't need to create a batch script to submit the FSL script, so just type in your single sbatch command:

{% highlight bash %}
var=`date +"%Y%m%d-%H%M%S"`
mkdir -p ~/logfiles/$var
sbatch \
-o ~/logfiles/${var}/output.txt \
-e ~/logfiles/${var}/error.txt \
~/scripts/EDSD/tbss_1_preproc.sh
{% endhighlight %}

### tbss_2_reg

<div class="embed-container">
<iframe src="https://player.vimeo.com/video/183678376" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>
</div>

Second, volumes are nonlinearly warped to the FMRIB58_FA template, which is supplied with FSL, by use of local deformation procedures performed by FNIRT, a nonlinear registration toolkit using a b-spline representation of the registration warp field.

Copy the first job script to create a new job script:

{% highlight bash %}
cp ~/scripts/EDSD/tbss_1_preproc.sh ~/scripts/EDSD/tbss_2_reg.sh
{% endhighlight %}

Change `--time=02:00:00` and the last line of code to this instead:

{% highlight vim %}
tbss_2_reg -T
{% endhighlight %}

Use the following code to submit the job script:

{% highlight bash %}
var=`date +"%Y%m%d-%H%M%S"`
mkdir -p ~/logfiles/$var
sbatch \
-o ~/logfiles/${var}/output.txt \
-e ~/logfiles/${var}/error.txt \
~/scripts/EDSD/tbss_2_reg.sh
{% endhighlight %}

### tbss_3_postreg

<div class="embed-container">
<iframe src="https://player.vimeo.com/video/183678375" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>
</div>

Next tbss_3_postreg generates a mean FA volume of all participants. This mean FA volume is thinned to create a mean FA skeleton representing the centers of all common tracts.

Copy the first job script to create a new job script:

{% highlight bash %}
cp ~/scripts/EDSD/tbss_1_preproc.sh ~/scripts/EDSD/tbss_3_postreg.sh
{% endhighlight %}

Change the last line of code to this instead:

{% highlight vim %}
tbss_3_postreg -S
{% endhighlight %}

Submit the job script:

{% highlight bash %}
var=`date +"%Y%m%d-%H%M%S"`
mkdir -p ~/logfiles/$var
sbatch \
-o ~/logfiles/${var}/output.txt \
-e ~/logfiles/${var}/error.txt \
~/scripts/EDSD/tbss_3_postreg.sh
{% endhighlight %}

### tbss_4_prestats

The last TBSS script carries out the final steps necessary before you run the voxelwise cross-subject stats. It thresholds the mean FA skeleton image at the chosen threshold - a common value that works well is 0.2.

Copy the first job script to create a new job script:

{% highlight bash %}
cp ~/scripts/EDSD/tbss_1_preproc.sh ~/scripts/EDSD/tbss_4_prestats.sh
{% endhighlight %}

Change the last line of code to this instead:

{% highlight vim %}
tbss_4_prestats 0.2
{% endhighlight %}

Submit tbss_4_prestats:

{% highlight bash %}
var=`date +"%Y%m%d-%H%M%S"`
mkdir -p ~/logfiles/$var
sbatch \
-o ~/logfiles/${var}/output.txt \
-e ~/logfiles/${var}/error.txt \
~/scripts/EDSD/tbss_4_prestats.sh
{% endhighlight %}

**At this point you may be asking your self, "Why not just put all the tbss_1 through tbss_4 code in a single job script?" Perfectly great question to ask. When you submit a command line, it typically should wait until the process has completed before moving to the next line in your code. Unfortunately, FSL commands don't always play nice and wait, so while you submit your code for tbss_2 it will immediately try to run tbss_3 and ultimately fail!**

## Using non-FA Images in TBSS

<div class="embed-container">
<iframe src="https://player.vimeo.com/video/183678372" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>
</div>

After you have run the full TBSS analysis on your FA data, the results can be applied to the other diffusion metrics: RD, AD, and MD. First, create a new directory called RD (or any other name) in your TBSS analysis directory (the one that contains the existing origdata, FA and stats directories from the FA analysis).

{% highlight bash %}
cd ~/compute/analyses/EDSD/TBSS/
mkdir RD
mkdir AD
mkdir MD
{% endhighlight %}

Copy your non-FA images into these new directories, making sure that they are named exactly the same as the original FA images were (look in origdata to check the original names - and keep them exactly the same, even if they include FA, which can be confusing; e.g. if there is an image origdata/subj005_FA.nii.gz then you need an image RD/subj005_FA.nii.gz and this file should contain the RD data, even though it has FA in the name). Confusing no?

{% highlight bash %}
for subj in $(ls ~/compute/images/EDSD/); do
cp -v ~/compute/images/EDSD/${subj}/raw/dti_MD.nii.gz ~/compute/analyses/EDSD/TBSS/MD/${subj}_FA.nii.gz;
cp -v ~/compute/images/EDSD/${subj}/raw/dti_L1.nii.gz ~/compute/analyses/EDSD/TBSS/AD/${subj}_FA.nii.gz;
cd ~/compute/images/EDSD/${subj}/raw/
fslmaths dti_L2.nii.gz -add dti_L3.nii.gz -div 2 dti_RD.nii.gz;
cp -v ~/compute/images/EDSD/${subj}/raw/dti_RD.nii.gz ~/compute/analyses/EDSD/TBSS/RD/${subj}_FA.nii.gz;
done
{% endhighlight %}

Now, making sure that you are in your top working TBSS directory (the one that now contains FA, stats and RD subdirectories) run the tbss_non_FA script, telling it that the alternate data is called RD. This will apply the original nonlinear registration to the RD data, merge all subjects' warped RD data into a 4D file stats/all_RD, project this onto the original mean FA skeleton (using the original FA data to find the projection vectors), resulting in the 4D projected data stats/all_RD_skeletonised. You don't really need to generate a script to run this, because the dataset is small enough, for larger datasets you will need to run these commands within a job script:

{% highlight bash %}
cd ~/compute/analyses/EDSD/TBSS/
tbss_non_FA AD
tbss_non_FA MD
tbss_non_FA RD
{% endhighlight %}

Now you are ready to run voxelwise statistics!
