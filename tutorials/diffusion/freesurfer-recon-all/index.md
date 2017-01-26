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

**UPDATE TO FREESURFER 6.0**

Log into the supercomputer:

{% highlight bash %}
cd ~/build/
wget ftp://surfer.nmr.mgh.harvard.edu/pub/dist/freesurfer/6.0.0/freesurfer-Linux-centos6_x86_64-stable-pub-v6.0.0.tar.gz
tar -C ~/apps/ -xzvf freesurfer-Linux-centos6_x86_64-stable-pub-v6.0.0.tar.gz
{% endhighlight %}

The brainstem and hippocampal subfield modules in FreeSurfer 6.0 require the Matlab R2012 runtime. This runtime is free, and therefore NO MATLAB LICENSES ARE REQUIRED TO USE THESE PACKAGES.

{% highlight bash %}
export FREESURFER_HOME=/fslhome/<USERNAME>/apps/freesurfer
cd $FREESURFER_HOME
curl "http://surfer.nmr.mgh.harvard.edu/fswiki/MatlabRuntime?action=AttachFile&do=get&target=runtime2012bLinux.tar.gz" -o "runtime2012b.tar.gz"
tar xvf runtime2012b.tar.gz
rm $FREESURFER_HOME/runtime2012b.tar.gz
{% endhighlight %}

Make sure your ~/.bash_profile includes the following:

{% highlight bash %}
# FREESURFER
export FREESURFER_HOME=/fslhome/<USERNAME>/apps/freesurfer
source $FREESURFER_HOME/SetUpFreeSurfer.sh
{% endhighlight %}

The reason we need to run FreeSurfer is so we can use the data later for TRACULA. TRACULA (TRActs Constrained by UnderLying Anatomy) is a tool for automatic reconstruction of a set of major white-matter pathways from diffusion-weighted MR images. It uses global probabilistic tractography with anatomical priors. Prior distributions on the neighboring anatomical structures of each pathway are derived from an atlas and combined with the FreeSurfer cortical parcellation and subcortical segmentation of the subject that is being analyzed to constrain the tractography solutions. This obviates the need for user interaction, e.g., to draw ROIs manually or to set thresholds on path angle and length, and thus automates the application of tractography to large datasets.

## Full Cortical Parcellation

Full FreeSurfer parcellation involves many, many steps. These steps have been *conveniently* batched in a script called recon-all.

<div class="embed-container">
<iframe src="https://player.vimeo.com/video/182604509?byline=0&portrait=0" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>
</div>

### Batch Script

Because FreeSurfer takes 8+ hours, the process will have to be submitted with both a batch and job script. Create a script that will batch submit your job script:

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

**Note the additional option to process the hippocampal subregions**. In order to run this, you need to be running FreeSurfer v6.0 (see above). Copy and paste this code into your job script:

{% highlight bash %}
#!/bin/bash

#SBATCH --time=15:00:00   # walltime
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
-hippocampal-subfields-T1 \
-sd /fslhome/${var}/compute/analyses/EDSD/FreeSurfer/
{% endhighlight %}

The only required option to run recon-all is **-subjid**; however, some additional options have been added. Since we already have a NIfTI image, we can use the **-i** to select the NIfTI image. Use **-wsatlas** option to improve skull-stripping. To run all 31 steps, add the **-all** option. Finally, to specify the subject's directory, add the option **-sd** and path to directory. The default environmental variable, SUBJECTS_DIR will be used otherwise.

### Submit Jobs

{% highlight bash %}
mkdir -p ~/compute/analyses/EDSD/FreeSurfer/
var=`date +"%Y%m%d-%H%M%S"`
mkdir -p ~/logfiles/$var
sh ~/scripts/EDSD/freesurfer_batch.sh $var
{% endhighlight %}

<div class="embed-container">
<iframe src="https://player.vimeo.com/video/182604509?byline=0&portrait=0" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>
</div>

## Viewing Volumes with Freeview

In order to view the output, you will need to download a participant directory to your local computer. Note you must have FreeSurfer installed on your local computer in order to run the following code:

{% highlight bash %}
rsync -rauv \
intj5@ssh.fsl.byu.edu:~/compute/analyses/EDSD/FreeSurfer/FRE_AD001 \
~/Desktop/
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
