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
3. Run and describe the 4 steps of a TBSS analysis

## Tract Based Spatial Statistics (TBSS)

Diffusion weighted imaging (DWI) measures the direction of water diffusion in brain tissue and is thought to be an indicator of the fiber tract integrity: reflecting coherence, organization and/or density of the fiber bundles. The most common standard diffusion measure is fractional anisotropy (FA). The value of FA, which ranges from 0 to 1, is highest in major white matter tracts (FA value approaches 1), lower in gray matter, and approaches 0 in cerebrospinal fluid. FA is thought to be a marker of white matter integrity, because variations reflect myelination, axon density, axonal membrane integrity, and axon diameter. Axial diffusivity (AD) represents diffusivity along the principal eigenvector. In contrast, radial diffusivity (RD) describes an average of the eigenvectors perpendicular to the principal direction.

TBSS is an automated method that tries to combine the strengths of both voxel-based morphometry (VBM) and tract-based spatial statistic methods. In VBM analyses, each participant’s FA image is registered to a template and then voxelwise statistics are computed to find brain areas that differ between groups. One major limitation to VBM methods is that coregistration algorithms are not able to accurately align fiber tracts across participants. This has been a long-standing issue with any VBM analysis. There is no way to ensure that a voxel in standard space contains data from the same portion of the brain across participants. Long projection white matter fiber tracts vary too much in size and shape and there is no way to be sure that registration of every participant’s FA image to a common space has been completely successful. TBSS tries to overcome these limitations of VBM, but TBSS still relies on registering participants to a common template space and only provides a modest improvement over traditional VBM methods. TBSS techniques also cannot ensure that any voxel corresponds to the same tract across participants. Notwithstanding these limitations, TBSS analyses remain the most common approach to analyzing diffusion weighted images.

## Preprocessing DWI

Each diffusion-weighted image is registered to the non-diffusion-weighted (b=0) using linear image registration for motion correction using FSL eddy_correct program. After correction, diffusion weighted images are skull-stripped in order to exclude non-brain voxels from all analyses using FSL bet program. Last, the diffusion tensors are calculated with the FSL DTIfit program for whole brain volumes and resulting FA, RD, MD, and AD volumes were used in tract-based spatial statistics analysis.

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

Prepare your FA data in your TBSS working directory in the right format:

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

Second, volumes are nonlinearly warped to the FMRIB58_FA template, which is supplied with FSL, by use of local deformation procedures performed by FNIRT, a nonlinear registration toolkit using a b-spline representation of the registration warp field.

Create a job script:

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
~/scripts/EDSD/tbss_2_preproc.sh
{% endhighlight %}

### tbss_3_postreg

Next tbss_3_postreg generates a mean FA volume of all participants. This mean FA volume is thinned to create a mean FA skeleton representing the centers of all common tracts.

Create a job script:

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

Create a job script:

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
