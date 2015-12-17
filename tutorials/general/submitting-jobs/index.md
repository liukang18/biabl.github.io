---
layout: tutorials
title: Submitting Batch Jobs on Supercomputer
author: naomi  
comments: true
date: 2015-12-09
---

## Objectives

After you complete this section, you should be able to:

1. Resources available via the supercomputer
2. Understand batch job submission and required parameters
3. SLURM commands
4. Generate your own script for job submission
5. Read output and standard error files generated from script
6. Submit jobs for many participants

Note that everything in "<>" is to be replaced. For example, \<fileName\> --> iLovePeanuts.txt

## FSL Resources

* The Fulton Supercomputing Lab offers a total of 16,928 CPU cores and about 50 TB of memory across 943 compute nodes.
* Compute resources are supported by approximately 2 petabytes of storage.
* All nodes have Red Hat Enterprise Linux 6.6.

## Job Submission

The FSL is managed by a batch scheduling system. This means that in order to use the supercomputer, users must write a script that encapsulates the workload into a non-interactive job. This script is then submitted as a job to the scheduling system. There are certain parameters required for the scheduling system:

* Either number of nodes and processors per node, or total number of processors
* Memory/RAM needed
* Expected running time (or "walltime")

Jobs are created by building a job script, usually written in bash or python, that specifies the necessary parameters described above, and then contains appropriate scripting to launch the program that will do the job's work.

## Scheduling System

The scheduling system keeps track of the current state of all resources and jobs and decides, based on conditions and policies, where and when to start jobs. Jobs are started in priority order until no further jobs are eligible to start or it runs out of appropriate resources.

When the scheduling system chooses to start a job, it assigns it one or more nodes/processors, as requested by the job, and launches the provided job script on the first node in the list of those assigned. The responsibility of taking advantage of all the requested resources is left up to the job script.

## SLURM Commands

* `sbatch` is used to submit jobs
* `squeue -u <username>` is used to show your queue
* `scancel <jobid>` cancels a specific job
* `scancel -u <username>` cancels all jobs for that user
* `sacct` shows current and historical job information 

## Example Job Script

{% highlight bash %}
#!/bin/bash

#SBATCH --time=50:00:00   # walltime
#SBATCH --ntasks=1  # number of processor cores (i.e. tasks)
#SBATCH --nodes=1   # number of nodes
#SBATCH --mem-per-cpu=8192M   # memory per CPUS core
#SBATCH -o /fslhome/<username>/logfiles/20151029/output0.txt
#SBATCH -e /fslhome/<username>/examples/20151029/error0.txt
#SBATCH -J "antsCorticalThickness0"   # job name

# Compatibility variables for PBS. Delete if not needed.
export PBS_NODEFILE=`/fslapps/fslutils/generate_pbs_nodefile`
export PBS_JOBID=$SLURM_JOB_ID
export PBS_O_WORKDIR="$SLURM_SUBMIT_DIR"
export PBS_QUEUE=batch

# Set the max number of threads to use for programs using OpenMP.
export OMP_NUM_THREADS=$SLURM_CPUS_ON_NODE

# LOAD MODULES, SET ENVIRONMENTAL VARIABLES HERE
export ANTSPATH=/fslhome/<username>/apps/ants/bin/
PATH=${ANTSPATH}:${PATH}

# INSERT CODE, AND RUN YOUR PROGRAMS HERE
subjDir=/fslhome/<username>/compute/data/1304_082907
templateDir=/fslhome/<username>/templates/OASIS

antsCorticalThickness.sh \
-d 3 \
-a ${subjDir}/n4.nii.gz \ 
-e ${templateDir}/T_template0.nii.gz \
-t ${templateDir}/T_template0_BrainCerebellum.nii.gz \
-m ${templateDir}/T_template0_BrainCerebellumProbabilityMask.nii.gz \ 
-f ${templateDir}/T_template0_BrainCerebellumExtractionMask.nii.gz \ 
-p ${templateDir}/Priors2/priors%d.nii.gz \
-q 1 \
-o ${subjDir}/antsCorticalThickness/
{% endhighlight %}

## Read Output / Error files

When you run batch commands, the output is sent to a text file instead of appearing on your screen. As with everything else, organization is needed in order to manage these output files. Especially if you are running a study with hundreds of participants. You'll have hundreds of output and error files!

### *vi* versus *cat*

*Cat* is the main command to read and print out the standard output and error files. When you *just* want to display the contents of a file, use `cat`. *Cat* is the most convenient program from displaying the contents of a text file.

On the other hand, if you want to edit a text file, use *vi* or *nano*, which are specifically text editors. 

To check the output from your process, type:

{% highlight bash %}
cat <output>.txt
{% endhighlight %}

If your script ends early, type:

{% highlight bash %}
cat <error>.txt
{% endhighlight %}  

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