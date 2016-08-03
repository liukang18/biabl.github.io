---
layout: tutorials
title: How to Submit Batch Jobs on Supercomputer
author: naomi  
comments: true
date: 2015-12-09
---

## Submitting a Job for Many Participants

In order to submit a job for many participants, you could either generate a job script for each participant. However, if you have hundreds or thousands of participants, it does not make sense to generate that many job script files. Another alternative is to use a for loop:

### BatchScript.sh

{% highlight bash %}
#!/bin/bash

for subj in $(ls ~/path/to/subject/directories/); do
sbatch \
-o ~/path/to/logfiles/output_${subj}.txt \
-e ~/path/to/logfiles/error_${subj}.txt \
~/path/to/jobScript.sh \
${subj}
sleep 1
done
{% endhighlight %}

This *for* loop, loops through all your directories setting the output and error text file with the subject directory name and submitting the jobScript.sh file with the input variable ${subj}. By using ${1} in your job script, you'll be able to loop through all your participants.

### JobScript.sh

{% highlight bash %}
#!/bin/bash

#SBATCH --time=50:00:00   # walltime
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

# LOAD MODULES AND SET ENVIRONMENTAL VARIABLES
export ANTSPATH=~/apps/ants/bin/
PATH=${ANTSPATH}:${PATH}

# INSERT CODE, AND RUN YOUR PROGRAMS HERE
SUBJ_DIR=~/path/to/subject/directories/${1}
TEMPLATE_DIR=~/templates/NKI10andUnder
OUT_DIR=${SUBJ_DIR}/antsCorticalThickness
~/apps/ants/bin/antsCorticalThickness.sh -d 3 \
-a ${SUBJ_DIR}/t1/resampled1iso.nii.gz \
-e ${TEMPLATE_DIR}/T_template0.nii.gz \
-t ${TEMPLATE_DIR}/T_template0_BrainCerebellum.nii.gz \
-m ${TEMPLATE_DIR}/T_template0_BrainCerebellumProbabilityMask.nii.gz \
-f ${TEMPLATE_DIR}/T_template0_BrainCerebellumExtractionMask.nii.gz \
-p ${TEMPLATE_DIR}/Priors2/priors%d.nii.gz \
-q 1 \
-o ${OUT_DIR}/
{% endhighlight %}

Then when you want to submit job scripts for all your participants, you simply have to submit your BatchScript.sh script:

{% highlight bash %}
sh ~/path/to/BatchScript.sh
{% endhighlight %}

### Additional Classroom Materials

* [Lecture](presentation)
* [Assignment Part 1](assignment-part1)
* [Assignment Part 2](assignment-part2)
