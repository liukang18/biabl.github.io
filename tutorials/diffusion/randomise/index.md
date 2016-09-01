---
layout: tutorials
title: Voxelwise Statistics
author: naomi
comments: true
date: 2016-08-02
---

## Objectives

After you complete this section, you should be able to:

1. Complete group statistics on FA, RD, AD, and MD.
2. View results

## Voxelwise Statistics

Before we perform statistics, we need to copy our non-FA images to our analysis directory:

{% highlight bash %}
mkdir -p ~/compute/analyses/EDSD/TBSS/MD
mkdir -p ~/compute/analyses/EDSD/TBSS/AD
mkdir -p ~/compute/analyses/EDSD/TBSS/RD

for i in $(ls ~/compute/images/EDSD/); do
cp -v ~/compute/images/EDSD/${i}/raw/dti_MD.nii.gz ~/compute/analyses/EDSD/TBSS/MD/${i}_FA.nii.gz;
cp -v ~/compute/images/EDSD/${i}/raw/dti_L1.nii.gz ~/compute/analyses/EDSD/TBSS/AD/${i}_FA.nii.gz;
fslmaths ~/compute/images/EDSD/${i}/raw/dti_L2.nii.gz -add ~/compute/images/EDSD/${i}/raw/dti_L3.nii.gz -div 2 ~/compute/images/EDSD/${i}/raw/dti_RD.nii.gz;
cp -v ~/compute/images/EDSD/${i}/raw/dti_RD.nii.gz ~/compute/analyses/EDSD/TBSS/RD/${i}_FA.nii.gz;
done
{% endhighlight %}

Your base job script will contain the following. You will need to create four more jobs scripts:

{% highlight bash %}
touch ~/scripts/EDSD/voxelwise-group-AD.sh
touch ~/scripts/EDSD/voxelwise-group-FA.sh
touch ~/scripts/EDSD/voxelwise-group-MD.sh
touch ~/scripts/EDSD/voxelwise-group-RD.sh
{% endhighlight %}

### FA

{% highlight bash %}
#!/bin/bash

#SBATCH --time=30:00:00   # walltime
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
randomise -i all_FA_skeletonised -o tbss_FA -m mean_FA_skeleton_mask -d design.mat -t design.con -n 5000 --T2 -V
{% endhighlight %}
