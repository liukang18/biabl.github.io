---
layout: tutorials
title: How to Submit a Single Job on Supercomputer
author: naomi  
comments: true
date: 2016-08-02
---

## Objectives

After you complete this section, you should be able to:

1. Understand batch job submission and required parameters
2. Generate your own script for job submission
3. Submit a job script
4. SLURM commands
5. Read output and standard error files generated from script

## Job Submission

The FSL is managed by a batch scheduling system. This means that in order to use the supercomputer, users must write a script that encapsulates the workload into a non-interactive job. This script is then submitted as a job to the scheduling system. There are certain parameters required for the scheduling system:

* Either number of nodes and processors per node, or total number of processors
* Memory/RAM needed
* Expected running time (or "walltime")

Jobs are created by building a job script, usually written in bash or python, that specifies the necessary parameters described above, and then contains appropriate scripting to launch the program that will do the job's work.

## Scheduling System

<div class="embed-container">
  <iframe src="https://www.youtube.com/embed/h8TZokyI6yo" frameborder="0" allowfullscreen></iframe>
</div>

The scheduling system keeps track of the current state of all resources and jobs and decides, based on conditions and policies, where and when to start jobs. Jobs are started in priority order until no further jobs are eligible to start or it runs out of appropriate resources.

When the scheduling system chooses to start a job, it assigns it one or more nodes/processors, as requested by the job, and launches the provided job script on the first node in the list of those assigned. The responsibility of taking advantage of all the requested resources is left up to the job script.

## How to Submit a Single Job on Supercomputer

<div class="embed-container">
  <iframe src="https://drive.google.com/file/d/0B7gwoaKa2xaTN1JkOWhRTlQySWs/preview"></iframe>
</div>

### Job Script

Let's create a simple job script:

{% highlight bash %}
vi ~/scripts/class/hostname.sh
{% endhighlight %}

Copy and paste the following code:

{% highlight bash %}
#!/bin/bash

#SBATCH --time=00:05:00   # walltime
#SBATCH --ntasks=1   # number of processor cores (i.e. tasks)
#SBATCH --nodes=1   # number of nodes
#SBATCH --mem-per-cpu=100M  # memory per CPU core

# COMPATABILITY VARIABLES FOR PBS. DO NO DELETE.
export PBS_NODEFILE=`/fslapps/fslutils/generate_pbs_nodefile`
export PBS_JOBID=$SLURM_JOB_ID
export PBS_O_WORKDIR="$SLURM_SUBMIT_DIR"
export PBS_QUEUE=batch
export OMP_NUM_THREADS=$SLURM_CPUS_ON_NODE

# LOAD MODULES, SET ENVIRONMENTAL VARIABLES HERE

# INSERT CODE, AND RUN YOUR PROGRAMS HERE
hostname
{% endhighlight %}

### Submit Job

When you run batch commands, the output is sent to a text file instead of appearing on your screen. As with everything else, organization is needed in order to manage these output files. Especially if you are running a study with hundreds of participants. You'll have hundreds of output and error files!

{% highlight bash %}
mkdir ~/logfiles
{% endhighlight %}

Instead of placing the output and error file location in the job script, include them in the SBATCH command when submitting a job script:

{% highlight bash %}
sbatch \
-o ~/logfiles/output-hostname.txt \
-e ~/logfiles/error-hostname.txt \
~/scripts/class/hostname.sh
{% endhighlight %}

## SLURM Commands

* `sbatch` is used to submit jobs
* `squeue -u <username>` is used to show your queue
* `scancel <jobid>` cancels a specific job
* `scancel -u <username>` cancels all jobs for that user
* `sacct` shows current and historical job information

## *vi* versus *cat*

*Cat* is the main command to read and print out the standard output and error files. When you *just* want to display the contents of a file, use `cat`. *Cat* is the most convenient program from displaying the contents of a text file.

On the other hand, if you want to edit a text file, use *vi*, which are specifically text editors.

To check the output from your process, type:

{% highlight bash %}
cat ~/logfiles/output-hostname.txt
{% endhighlight %}

If your script ends early, type:

{% highlight bash %}
cat ~/logfiles/error-hostname.txt
{% endhighlight %}

## Class Slides

<div class="embed-container">
  <iframe src="//slides.com/njhunsak/preprocessing-t1-images-2-3/embed" scrolling="no" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>
</div>
