---
layout: tutorials
title: FreeSurfer recon-all
author: naomi
comments: true
date: 2016-09-01
---

## Objectives

After you complete this section, you should be able to:

1. Perform full cortical reconstruction, parcellation, and labeling using FreeSurfer
2. View brain volumes in 2D: brain mask, white matter mask, and subcortical segmentation
3. View surfaces in 3D: pial, white and inflated surface; sulcal and curvature maps; thickness maps; cortical parcellation

## Before You Begin

Edit your ~/.bash_profile to include:

{% highlight bash %}
# FREESURFER
export FREESURFER_HOME=/fslhome/USERNAME/apps/freesurfer
source $FREESURFER_HOME/SetUpFreeSurfer.sh
{% endhighlight %}

## Full Cortical Parcellation

Full FreeSurfer parcellation involves many, many steps. These steps have been *conveniently* batched in a script called recon-all.

### Batch Script

Because FreeSurfer takes 24+ hours, the process will have to be submitted with both a batch and job script. Create a script that will batch submit your job script:

{% highlight bash %}
vi ~/scripts/EDSD/freesurfer_batch.sh
{% endhighlight %}

Copy and paste this code into your batch script:

{% highlight bash %}
#!/bin/bash

for subj in $(ls ~/compute/images/EDSD/); do
sbatch \
-o ~/logfiles/${1}/output_${subj}.txt \
-e ~/logfiles/${1}/error_${subj}.txt \
~/scripts/EDSD/freesurfer_job.sh \
${subj}
sleep 1
done
{% endhighlight %}

### Job Script

Create a job script:

{% highlight bash %}
vi ~/scripts/EDSD/freesurfer_job.sh
{% endhighlight %}

Copy and paste this code into your job script:

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
export FREESURFER_HOME=/fslhome/${var}/apps/freesurfer
source $FREESURFER_HOME/SetUpFreeSurfer.sh

# INSERT CODE, AND RUN YOUR PROGRAMS HERE
~/apps/freesurfer/bin/recon-all \
-subjid ${1} \
-i /fslhome/${var}/compute/images/EDSD/${1}/t1/resampled.nii.gz \
-wsatlas \
-all \
-sd /fslhome/${var}/compute/analyses/EDSD/FreeSurfer/
{% endhighlight %}

### Submit Jobs

{% highlight bash %}
mkdir -p ~/compute/analyses/EDSD/FreeSurfer/
var=`date +"%Y%m%d-%H%M%S"`
mkdir -p ~/logfiles/$var
sh ~/scripts/EDSD/freesurfer_batch.sh $var
{% endhighlight %}

## Viewing Volumes with Freeview

In order to view the output, you will need to download a participant directory to your local computer. Note you must have FreeSurfer installed on your local computer in order to run the following code:

{% highlight bash %}
rsync -rauv intj5@ssh.fsl.byu.edu:~/compute/analyses/EDSD/FreeSurfer/FRE_AD001 ~/Desktop/
{% endhighlight %}

With one Freeview command line, you can load several output volumes, such as brainmask.mgz and wm.mgz; the surfaces, rh.white and lh.white; and the subcortical segmentation, aseg.mgz. Copy and paste the command below inside the terminal window and press enter:

{% highlight bash %}
cd ~/Desktop/
freeview -v \
FRE_AD001/mri/T1.mgz \
FRE_AD001/mri/wm.mgz \
FRE_AD001/mri/brainmask.mgz \
FRE_AD001/mri/aseg.mgz:colormap=lut:opacity=0.2 \
-f FRE_AD001/surf/lh.white:edgecolor=blue \
FRE_AD001/surf/lh.pial:edgecolor=red \
FRE_AD001/surf/rh.white:edgecolor=blue \
FRE_AD001/surf/rh.pial:edgecolor=red
{% endhighlight %}

For more information about the different views:

[http://biabl.github.io/tutorials/structural/freesurfer-recon-all/](http://biabl.github.io/tutorials/structural/freesurfer-recon-all/)

## Viewing Surfaces in 3D using Freeview

With one Freeview command line, you can also view several surface volumes, such as pial, white and inflated surface; thickness maps; sulcal and curvature maps; and cortical parcellation. You can load them all in Freeview with the command below (be patient while they all load). The follow command only loads the left hemisphere, however you could also just view the right hemisphere or both hemispheres at the same time:

{% highlight bash %}
freeview -f  FRE_AD001/surf/lh.pial:annot=aparc.annot:name=pial_aparc:visible=0 \
FRE_AD001/surf/lh.inflated:overlay=lh.thickness:overlay_threshold=0.1,3::name=inflated_thickness:visible=0 \
FRE_AD001/surf/lh.inflated:visible=0 \
FRE_AD001/surf/lh.white:visible=0 \
FRE_AD001/surf/lh.pial \
--viewport 3d
{% endhighlight %}

For more information about the different views:

[http://biabl.github.io/tutorials/structural/freesurfer-recon-all/](http://biabl.github.io/tutorials/structural/freesurfer-recon-all/)
