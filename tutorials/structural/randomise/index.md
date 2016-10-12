---
layout: tutorials
title: Randomise
author: naomi
comments: true
date: 2016-10-12
---

## Objectives

After you complete this section, you should be able to:

1.

## Before You Begin

### Copy Files

{% highlight bash %}
mkdir -p ~/compute/analyses/class/CT/data/
for subj in $(ls ~/compute/class/); do
cp -v \
~/compute/class/${subj}/antsCT/CorticalThicknessNormalizedToTemplate.nii.gz \
~/compute/analyses/class/CT/data/${subj}_CT.nii.gz
done
{% endhighlight %}

### Smoothing

{% highlight bash %}
for file in $(ls ~/compute/analyses/class/VBM/data); do
~/apps/ants/bin/SmoothImage \
3 \
~/compute/analyses/class/VBM/data/${file} \
1 \
~/compute/analyses/class/VBM/data/${file}
done
{% endhighlight %}

### Create 4D Image

{% highlight bash %}
cd ~/compute/analyses/class/CT && mkdir stats
fslmerge -t stats/merged.nii.gz data/*.nii.gz
{% endhighlight bash %}

### Mask

{% highlight bash %}
cp -v \
~/templates/class/template_BrainCerebellumMask.nii.gz \
~/compute/analyses/class/CT/stats/
{% endhighlight %}

<img class="img-responsive" alt="" src="images/modulated.png">

## Analysis Design

Before running **randomise** you will need to generate a design matrix file, e.g., **design.mat** and a contrasts file, e.g., **design.con**. Note that the order of the entries (rows) in your design matrix must match the alphabetical order of your original FA images, as that determines the order of the images in the final 4D file merged.nii.gz; check this with:

{% highlight bash %}
cd ~/compute/analyses/class/CT/data
imglob *.*
{% endhighlight %}

Luckily for our dataset, the first 10 participants are children who have sustained a traumatic brain injury and the last 10 participants are orthopedic controls. If your participants are not organized by group, you will need to be extra careful when creating your design matrix.

### Design.mat

To create your design matrix, start by creating a **stats** directory.

{% highlight bash %}
mkdir -p ~/compute/analyses/class/CT/stats
{% endhighlight %}

Nex, create a text file within your **stats** directory. Each column in the text file represents an explanatory variable (EV) and each row represents a participant. In our design matrix example, the first column represents the traumatic brain injury variable and the second column represents the orthopedic control variable. Looking at the text file contents below, can you imagine if your list of participants weren't organized by group, how meticulous you would need to be to make sure each row corresponded perfectly with the correct participant?

{% highlight bash %}
vi ~/compute/analyses/class/CT/stats/design.txt
{% endhighlight %}

Copy and paste into the design.txt file:

{% highlight vim %}
1	0
1	0
1	0
1	0
1	0
1	0
1	0
1	0
1	0
1	0
0	1
0	1
0	1
0	1
0	1
0	1
0	1
0	1
0	1
0	1
{% endhighlight %}

Save these data as design.txt, then run the following:

{% highlight bash %}
cd ~/compute/analyses/class/CT/stats/
Text2Vest design.txt design.mat
{% endhighlight %}

This will convert the design matrix data into the format used by the FSL tools, and save the matrix as design.mat.

### Design.con

Contrast files must have one row for each contrast, and a column for each EV. For example, we have 2 EVs in our design matrix above. A contrast matrix for this design might look like this (four contrasts, first two comparing the two groups and the last two giving the mean for each group in the study):

{% highlight bash %}
1 -1
-1 1
1 0
0 1
{% endhighlight %}

Copy and paste the constrasts into a new file:

{% highlight bash %}
vi ~/compute/analyses/class/CT/stats/contrasts.txt
{% endhighlight %}

After you save the contrasts.txt file, simply run:

{% highlight bash %}
Text2Vest contrasts.txt design.con
{% endhighlight %}

Additionally, if you want to keep things easy to remember during the analyses, add the follow lines to the top of your design.con file:

{% highlight vim %}
/ContrastName1	TBI > OI
/ContrastName2	OI > TBI
/ContrastName3	mean TBI
/ContrastName4	mean OI
{% endhighlight %}

## Voxelwise Statistics

**randomise** is FSL's tool for nonparametric permutation inference on neuroimaging data. **randomise** allows modeling and inference using standard GLM design setup as used for example in FEAT. It can output voxelwise, cluster-based and TFCE-based tests, and also offers variance smoothing as an option. For more information about FSL's tool, [click here for the user guide](http://fsl.fmrib.ox.ac.uk/fsl/fslwiki/Randomise/UserGuide).

Create a job script:

{% highlight bash %}
vi ~/scripts/class/CT_voxelwise.sh
{% endhighlight %}

Copy and paste into the job script:

{% highlight bash %}
#!/bin/bash

#SBATCH --time=05:00:00   # walltime
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
cd ~/compute/analyses/class/CT/stats/
randomise \
-i merged.nii.gz \
-o TBIvsOI \
-m template_BrainCerebellumMask \
-d design.mat \
-t design.con \
-n 5000 \
-T -V
{% endhighlight %}

### Submit Job Script

When you submit the job script, you will need to include two command line arguments. The first, needs to be the name of your group skeletonised image, the second needs to be your output prefix. In the following code, if you create an array **DTI** that contains the four DTI metrics, then you can use a for loop and loop through the array **DTI** and submit four job scripts, one for each DTI measure:

{% highlight bash %}
var=`date +"%Y%m%d-%H%M%S"`
mkdir -p ~/logfiles/${var}
sbatch \
-o ~/logfiles/${var}/output.txt \
-e ~/logfiles/${var}/error.txt \
~/scripts/class/CT_voxelwise.sh
{% endhighlight %}
