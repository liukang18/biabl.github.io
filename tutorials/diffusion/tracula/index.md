---
layout: tutorials
title: Tracts Constrained By Underlying Anatomy
author: naomi
comments: true
date: 2016-11-02
---

<img class="img-responsive" alt="" src="images/tracula.jpg">

## TRACULA

TRACULA uses the prior anatomical information derived from the cortical parcellation and subcortical segmentation obtained from FreeSurfer. Let us make sure you have the latest version of TRACULA:

{% highlight bash %}
cd ~/build
wget ftp://surfer.nmr.mgh.harvard.edu/pub/dist/freesurfer/5.3.0-tracula-addons/tracula.update.centos6_x86_64.5.3.2014_05_26.tar.gz
tar xvf tracula.update.centos6_x86_64.5.3.2014_05_26.tar.gz
cp -v tracula.update.centos6_x86_64.5.3.2014_05_26/* ~/apps/freesurfer/bin/
{% endhighlight %}

## Configuration File

A configuration file for each participant needs to be generated using the DTI image (i.e., dti.nii.gz), bvec (i.e., dti.bvec) and bval (i.e., dti.bval) files output from dcm2nii. Unfortunately, the parallel processing tools in FSL don't work on our supercomputer, so we have to get creative and make this process run across compute nodes and not on a single node. Start by creating an analyses directory:

{% highlight bash %}
mkdir -p ~/compute/analyses/EDSD/TRACULA/data
{% endhighlight %}

Next we need to create a template configuration file:

{% highlight bash %}
vi ~/compute/analyses/EDSD/TRACULA/FRE_AD001.config
{% endhighlight %}

Copy and past this into the configuration file:

{% highlight bash %}
setenv SUBJECTS_DIR /fslhome/intj5/compute/analyses/EDSD/FreeSurfer/

set dtroot = /fslhome/intj5/compute/analyses/EDSD/TRACULA/data/

set subjlist = ( FRE_AD001 )

set dcmroot = /fslhome/intj5/compute/images/EDSD/

set dcmlist = ( FRE_AD001/raw/dti.nii.gz )

set bveclist = ( /fslhome/intj5/compute/images/EDSD/FRE_AD001/raw/dti.bvec )

set bvalfile = /fslhome/intj5/compute/images/EDSD/FRE_AD001/raw/dti.bval
{% endhighlight %}

Now that we have one configuration file, using the **sed** command, we can find and replace **FRE_AD001** with the remaining participant IDs to create configuration files for all the participants:

{% highlight bash %}
subjects=( FRE_AD001 FRE_AD002 FRE_AD003 FRE_AD004 FRE_AD005 FRE_AD006 FRE_AD007 FRE_AD008 FRE_AD009 FRE_AD010 FRE_HC001 FRE_HC002 FRE_HC003 FRE_HC004 FRE_HC005 FRE_HC006 FRE_HC007 FRE_HC008 FRE_HC009 FRE_HC010 FRE_HC011 FRE_HC012 FRE_HC013 FRE_HC014 FRE_HC015 FRE_HC016 )
for (( i=0; i<${#subjects[@]}; i++ )); do
sed "s/${subjects[$i]}/${subjects[$i+1]}/g" ${subjects[$i]}.config > ${subjects[$i+1]}.config;
done
{% endhighlight %}

## Preprocessing

The preprocessing workflow of TRACULA is based on a standardized routine that utilizes tools available in the software library of FSL. Image distortions induced by eddy currents and head motion are corrected by aligning each image to the mean non-diffusion-weighted (b=0) using linear image registration using FSL eddy_correct program. After correction, diffusion weighted images are skull-stripped in order to exclude non-brain voxels from all analyses using FSL bet program. Once skull-stripped, a diffusion tensor model was fit at each voxel, generating fractional anisotropy (FA) maps using FSL DTIfit program. Although we've already completed these steps, the TRACULA pipeline redoes them for us. Once diffusion-weighted images are processed, the individual anatomical T1-weighted images are aligned to the b=0 images, and then all participants’ images are aligned to the MNI152 atlas through affine registration. Note that affine registration occurs and not diffeomorphic. Affine registration is used because it places all the participant images in a common space.

The job script for this is pretty easy and straight forward. The program **trac-all** will run the preprocessing steps **-pre** using the supplied configuration file. If you notice there's a command line variable **${1}** in the name of the configuration file, so we'll be able to use participant ID as the variable to submit a job script for each participant!

{% highlight bash %}
vi ~/scripts/EDSD/tracula-prep-job.sh
{% endhighlight %}

Copy and paste the following code into the script:

{% highlight vim %}
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
export FREESURFER_HOME=/fslhome/${var}/apps/freesurfer
source $FREESURFER_HOME/SetUpFreeSurfer.sh

# INSERT CODE, AND RUN YOUR PROGRAMS HERE
cd ~/compute/analyses/EDSD/TRACULA/
~/apps/freesurfer/bin/trac-all -prep -c ~/compute/analyses/EDSD/TRACULA/${1}.config
{% endhighlight %}

The batch script is just like we've had previously:

{% highlight bash %}
vi ~/scripts/EDSD/tracula-prep-batch.sh
{% endhighlight %}

Copy and paste the following code:

{% highlight vim %}
#!/bin/bash

for subj in $(ls ~/compute/images/EDSD); do
sbatch \
-o ~/logfiles/${1}/output_${subj}.txt \
-e ~/logfiles/${1}/error_${subj}.txt \
~/scripts/EDSD/tracula-prep-job.sh \
${subj}
sleep 1
done
{% endhighlight %}

To submit the batch script:

{% highlight bash %}
var=`date +"%Y%m%d-%H%M%S"`
mkdir -p ~/logfiles/$var
sh ~/scripts/EDSD/tracula-prep-batch.sh $var
{% endhighlight %}

## Reconstruct Pathways

FSL’s bedpostX is used to apply the ball- and-stick model of diffusion. TRACULA then uses the individual participant’s ball-and-stick model of local diffusion orientations, as well as the participant’s cortical and subcortical segmentation labels, combined with prior information of each tract to estimate the probability distributions of each tract. Eighteen major pathways are reconstructed: corticospinal tract, inferior longitudinal fasciculus, uncinate fasciculus, anterior thalamic radiation, cingulum-cingulate gyrus bundle, cingulum-angular bundle, superior longitudinal fasciculus-parietal bundle, superior longitudinal fasciculus-temporal bundle, and forceps major and forceps minor of the corpus callosum.

{% highlight bash %}
vi ~/scripts/EDSD/tracula-bedp-job.sh
{% endhighlight %}

Copy and paste the following code into the script:

{% highlight vim %}
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
cd ~/compute/analyses/EDSD/TRACULA/
~/apps/freesurfer/bin/trac-all -bedp -c ~/compute/analyses/EDSD/TRACULA/${1}.config
{% endhighlight %}

The batch script is just like we've had previously:

{% highlight bash %}
vi ~/scripts/EDSD/tracula-bedp-batch.sh
{% endhighlight %}

Copy and paste the following code:

{% highlight vim %}
#!/bin/bash

for subj in $(ls ~/compute/images/EDSD); do
sbatch \
-o ~/logfiles/${1}/output_${subj}.txt \
-e ~/logfiles/${1}/error_${subj}.txt \
~/scripts/EDSD/tracula-bedp-job.sh \
${subj}
sleep 1
done
{% endhighlight %}

To submit the batch script:

{% highlight bash %}
var=`date +"%Y%m%d-%H%M%S"`
mkdir -p ~/logfiles/$var
sh ~/scripts/EDSD/tracula-bedp-batch.sh $var
{% endhighlight %}

## Probabilistic Distrubution of Pathways

The final step of TRACULA is to generate the probability distributions for each white-matter bundles specified in the configuration file. This is done by simultaneously fitting the shape of each pathway to the results of the ball-and-stick model of diffusion from above and to the prior knowledge of the pathway anatomy given by the set of manually labeled training subjects in the TRACULA atlas. The following command reconstructs the probabilistic distribution of the pathways:

{% highlight bash %}
vi ~/scripts/EDSD/tracula-path-job.sh
{% endhighlight %}

Copy and paste the following code into the script:

{% highlight vim %}
#!/bin/bash

#SBATCH --time=10:00:00   # walltime
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
cd ~/compute/analyses/EDSD/TRACULA/
~/apps/freesurfer/bin/trac-all -path -c ~/compute/analyses/EDSD/TRACULA/${1}.config
{% endhighlight %}

The batch script is just like we've had previously:

{% highlight bash %}
vi ~/scripts/EDSD/tracula-path-batch.sh
{% endhighlight %}

Copy and paste the following code:

{% highlight vim %}
#!/bin/bash

for subj in $(ls ~/compute/images/EDSD); do
sbatch \
-o ~/logfiles/${1}/output_${subj}.txt \
-e ~/logfiles/${1}/error_${subj}.txt \
~/scripts/EDSD/tracula-path-job.sh \
${subj}
sleep 1
done
{% endhighlight %}

To submit the batch script:

{% highlight bash %}
var=`date +"%Y%m%d-%H%M%S"`
mkdir -p ~/logfiles/$var
sh ~/scripts/EDSD/tracula-path-batch.sh $var
{% endhighlight %}

## Quality Assessment

Since diffusion weighted imaging analyses are designed to detect the motion of water molecules, this makes diffusion MR images particularly sensitive to head motion. Head motion results in not only misalignment between consecutive diffusion weighted images, but also intensity changes. Misalignment can be corrected by registering the diffusion weighted images to each other; however, intensity alterations cannot be corrected. There are four measures of head motion outputted from the TRACULA pipeline: the average volume-by-volume translation and rotation, the percent of slices with excessive intensity drop-out, and the average drop-out score for slices with excessive intensity drop-out.

{% highlight bash %}
~/compute/analyses/EDSD/TRACULA/data/FRE_AD001/dmri/dwi_motion.txt
{% endhighlight %}

Participant FRE_AD001 was pretty well-behaved. These motion measures can be used to ensure that groups of participants are matched with respect to head motion or be used as nuissance regressor in group analyses:

{% highlight bash %}
AvgTranslation AvgRotation PercentBadSlices AvgDropoutScore
1.026 0.0100014 0 1
{% endhighlight %}

## Outputs

For each identified pathway, fractional anisotropy (FA), mean diffusivity (MD), radial diffusivity (RD), and axial diffusivity (AD) are extracted along the tract at the maximum position on the a posteriori path, as well averaged over all sample paths. Because tracts are reconstructed in each participant’s native diffusion space and not on a template, diffusion measures must be interpolated to corresponding positions along the tract for all participants. Once interpolated, some participants’ endpoints contained NA values, because the endpoints vary tremendously across participants.

Sync your data to a local computer with FreeSurfer installed:

{% highlight bash %}
rsync -rauv \
intj5@ssh.fsl.byu.edu:~/compute/analyses/EDSD/TRACULA \
~/Desktop/
{% endhighlight %}

<img class="img-responsive" alt="" src="images/FA.png">

You can use freeview to view the FA image for each participant in native space. You can do this with the command below:

{% highlight bash %}
cd ~/Desktop/TRACULA/data/FRE_AD002
freeview dmri/dtifit_FA.nii.gz &
{% endhighlight %}

<img class="img-responsive" alt="" src="images/probability.png">

To visualize the probability distribution of single white-matter pathways, do the following:

{% highlight bash %}
freeview -v dmri/dtifit_FA.nii.gz \
dpath/rh.ilf_AS_avg33_mni_bbr/path.pd.nii.gz:colormap=jet:isosurface=0,0:color='Red':name=rh.ilf \
dpath/lh.ilf_AS_avg33_mni_bbr/path.pd.nii.gz:colormap=jet:isosurface=0,0:color='Red':name=lh.ilf
{% endhighlight %}

<img class="img-responsive" alt="" src="images/all.png">

To visualize the probability distribution of all white-matter pathways, do the following:

{% highlight bash %}
freeview -tv dpath/merged_avg33_mni_bbr.mgz \
-v dmri/dtifit_FA.nii.gz &
{% endhighlight %}

When you are done, close Freeview.
