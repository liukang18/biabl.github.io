---
layout: tutorials
title: How to Submit Batch Jobs on Supercomputer
author: naomi  
comments: true
date: 2016-08-11
---

## Objectives

After you complete this section, you should be able to:

1. Know how to set variables for arguments passed to the shell script
2. Write a batch script and edit job script to loop over many participants
3. Submit batch script on the Supercomputer

## Special Variables

<div class="embed-container">
  <iframe src="https://drive.google.com/file/d/0B7gwoaKa2xaTdU9wYXVDVndFTms/preview"></iframe>
</div>

Let's create a script:

{% highlight bash %}
vi ~/Desktop/printargs.sh
{% endhighlight %}

Copy and paste the following code into the new script:

{% highlight vim %}
#!/bin/bash                                                                                                        

echo "arg 0: $0"
echo "arg 1: $1"
echo "arg 2: $2"
echo "arg 3: $3"
echo "arg 4: $4"
{% endhighlight %}

Now run the script:

{% highlight bash %}
sh ~/Desktop/printargs.sh x y z
{% endhighlight %}

What do you end up with?

{% highlight bash %}
arg 0: /Users/njhunsaker/Desktop/printargs.sh
arg 1: x
arg 2: y
arg 3: z
arg 4:
{% endhighlight %}

Argument 0 always refers to the script, and each $1 refers to the arguments to the script. The script prints the first 4 arguments, but there isn't a fourth argument. $4 is treated as the empty string.

## Job Script for Every Participant

<div class="embed-container">

</div>

Consider the following job script:

{% highlight bash %}
#!/bin/bash

#SBATCH --time=06:00:00   # walltime
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
export ANTSPATH=/fslhome/$var/apps/ants/bin/
PATH=${ANTSPATH}:${PATH}

# INSERT CODE, AND RUN YOUR PROGRAMS HERE
DATA_DIR=~/compute/class/1304/
TEMPLATE_DIR=~/templates/class/
~/apps/ants/bin/antsCorticalThickness.sh \
-d 3 \
-a ${DATA_DIR}/t1/resampled.nii.gz \
-e ${TEMPLATE_DIR}/template.nii.gz \
-t ${TEMPLATE_DIR}/template_BrainCerebellum.nii.gz \
-m ${TEMPLATE_DIR}/template_BrainCerebellumProbabilityMask.nii.gz \
-f ${TEMPLATE_DIR}/template_BrainCerebellumExtractionMask.nii.gz \
-p ${TEMPLATE_DIR}/priors/priors%d.nii.gz \
-q 1 \
-o ${DATA_DIR}/antsCT/
{% endhighlight %}

You need to be able to set the DATA_DIR to a specific participant. What if you have a 100 participants? or 1,000? Are you going to edit and generate 100 or 1,000 job scripts and then try to individually submit each one? It does not make sense to generate that many job scripts, so here's how to run this job script within a for loop.

### Batch Script

Create a script that will batch submit your job script:

{% highlight bash %}
vi ~/scripts/class/antsCT_batch.sh
{% endhighlight %}

Copy and paste this code into your batch script:

{% highlight bash %}
#!/bin/bash

for subj in $(ls ~/compute/class/); do
sbatch \
-o ~/logfiles/${1}/output_${subj}.txt \
-e ~/logfiles/${1}/error_${subj}.txt \
~/scripts/class/antsCT_job.sh \
${subj}
sleep 1
done
{% endhighlight %}

Notice the `$1` in the script. Recall that this will refer to an argument that is passed along to the shell script when we submit the script, so make sure to include the timestamp as an argument.

{% highlight bash %}
var=`date +"%Y%m%d-%H%M%S"`
mkdir -p ~/logfiles/$var
sh ~/scripts/class/antsCT_batch.sh $var
{% endhighlight %}

The variable `$var` will be passed to the shell script as variable `$1`. This *for* loop, loops through all your directories setting the output and error text file with the subject directory name and submitting the antsCT_job.sh file with it's own special variable, `${subj}`. So when antsCT_job.sh runs, it will pass along the `${subj}` variable as `$1` in your antsCT_job.sh script.

### Job Script

Instead of setting the participant ID in the job script, simply replace the participant ID with the variable `$1` or `${1}`, which is the same thing. Remember that the batch script submitted a job script with the argument `${subj}`. Now whereever there is a `$1` in the job script, it will represent the subject ID.

{% highlight bash %}
#!/bin/bash

#SBATCH --time=06:00:00   # walltime
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
export ANTSPATH=/fslhome/$var/apps/ants/bin/
PATH=${ANTSPATH}:${PATH}

# INSERT CODE, AND RUN YOUR PROGRAMS HERE
DATA_DIR=~/compute/class/${1}/
TEMPLATE_DIR=~/templates/class/
~/apps/ants/bin/antsCorticalThickness.sh \
-d 3 \
-a ${DATA_DIR}/t1/resampled.nii.gz \
-e ${TEMPLATE_DIR}/template.nii.gz \
-t ${TEMPLATE_DIR}/template_BrainCerebellum.nii.gz \
-m ${TEMPLATE_DIR}/template_BrainCerebellumProbabilityMask.nii.gz \
-f ${TEMPLATE_DIR}/template_BrainCerebellumExtractionMask.nii.gz \
-p ${TEMPLATE_DIR}/priors/priors%d.nii.gz \
-q 1 \
-o ${DATA_DIR}/antsCT/
{% endhighlight %}

Then when you want to submit job scripts for all your participants, you simply have to submit your batch script, which will automatically submit your job scripts for each participant:

{% highlight bash %}
var=`date +"%Y%m%d-%H%M%S"`
mkdir -p ~/logfiles/$var
sh ~/scripts/class/antsCT_batch.sh $var
{% endhighlight %}

The output from this process is pure ***gold!***

## Class Slides

<div class="embed-container">
  <iframe src="//slides.com/njhunsak/submit-multiple-jobs/embed" scrolling="no" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>
</div>
