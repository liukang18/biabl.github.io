---
layout: tutorials
title: Voxelwise Statistics
author: naomi
comments: true
date: 2016-08-02
---

## Objectives

After you complete this section, you should be able to:

1. Setup design matrix and contrasts for a two-sample unpaired t-test
2. Perform voxelwise statistics using FSL's tool randomise
3. View and understand results

## Analysis Design

Before running **randomise** you will need to generate a design matrix file, e.g., **design.mat** and contrasts file, e.g., **design.con**. Note that the order of the entries (rows) in your design matrix must match the alphabetical order of your original FA images, as that determines the order of the aligned FA images in the final 4D file all_FA_skeletonised; check this with:

{% highlight bash %}
cd ~/compute/analyses/EDSD/TBSS/FA
imglob *_FA.*
{% endhighlight %}

Luckily for our dataset, the first 10 participants are patients with Alzheimer's disease and the last 16 participants are healthy controls.

### Design.mat

First, you need to create a text file that contains your design matrix. Each column is an explanatory variable (EV) and each row represents a participant. In our design matrix, the first column represents the Alzheimer's disease group and the second column represents the healthy control group.

{% highlight bash %}
vi ~/compute/analyses/EDSD/TBSS/stats/design.txt
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
0	1
0	1
0	1
0	1
0	1
0	1
{% endhighlight %}

All you need to do is save this data as design.txt (or any other file name you fancy), then run the following:

{% highlight bash %}
cd ~/compute/analyses/EDSD/TBSS/stats/
Text2Vest design.txt design.mat
{% endhighlight %}

This will convert the design matrix data into the format used by the FSL tools, and save the matrix as design.mat.

### Design.con

Contrast files must have one row for each contrast, and a column for each EV. For example, we have 2 EVs in our design matrix above. A contrast matrix for this design might look like this (four contrasts, first two giving the mean for each group in the study and the last two comparing the two groups):

{% highlight bash %}
1 0
0 1
1 -1
-1 1
{% endhighlight %}

If you save this as contrasts.txt, simply run this:

{% highlight bash %}
Text2Vest contrasts.txt design.con
{% endhighlight %}

If you want to keep things easy to remember during the analyses, add the follow lines to the top of your design.con file:

{% highlight vim %}
/ContrastName1	AD mean
/ContrastName2	HC mean
/ContrastName3	AD > HC
/ContrastName4	HC > AD
{% endhighlight %}

## Voxelwise Statistics

**randomise** is FSL's tool for nonparametric permutation inference on neuroimaging data. **randomise** allows modeling and inference using standard GLM design setup as used for example in FEAT. It can output voxelwise, cluster-based and TFCE-based tests, and also offers variance smoothing as an option. For more information about FSL's tool, [click here for the user guide](http://fsl.fmrib.ox.ac.uk/fsl/fslwiki/Randomise/UserGuide).

Create a job script:

{% highlight bash %}
vi ~/scripts/EDSD/tbss_voxelwise.sh
{% endhighlight %}

Copy and paste into the job script:

{% highlight bash %}
#!/bin/bash

#SBATCH --time=40:00:00   # walltime
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
cd ~/compute/analyses/EDSD/TBSS/stats/
randomise -i ${1} -o ${2} -m mean_FA_skeleton_mask -d design.mat -t design.con -n 5000 --T2 -V
{% endhighlight %}

### Submit Job Script

When you submit the job script, you will need to include two command line arguments. The first, needs to be the name of your group skeletonised image, the second needs to be your output prefix.

{% highlight bash %}
var=`date +"%Y%m%d-%H%M%S"`
mkdir -p ~/logfiles/$var
sbatch \
-o ~/logfiles/${var}/output.txt \
-e ~/logfiles/${var}/error.txt \
~/scripts/EDSD/tbss_voxelwise.sh \
all_FA_skeletonised \
tbss_FA
{% endhighlight %}

To analyze AD, use **all_AD_skeletonised** as your input file and **tbss_AD** as your output. To analyze RD, use **all_RD_skeletonised** as your input file and **tbss_RD** as your output. To analyze MD, use **all_AD_skeletonised** as your input file and **tbss_MD** as your output.

## Results

In order to view the results, you will need to download the TBSS stats directory to a local computer that has FSL installed:

{% highlight bash %}
rsync -rauv
{% endhighlight %}
