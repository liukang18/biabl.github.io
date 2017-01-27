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

## Hippocampal Subregion Analysis

Check that FreeSurfer ran completely with no errors, navigate to the logfile directory that contains the error and output files:

{% highlight bash %}
cd ~/logfiles/20170126-103140/
tail -n 5 output_FRE_*
{% endhighlight %}

You should see the following result from the `tail` command:

{% highlight bash %}
==> output_FRE_AD001.txt <==
Started at Thu Jan 26 10:34:48 MST 2017 
Ended   at Thu Jan 26 20:48:16 MST 2017
#@#%# recon-all-run-time-hours 10.224
recon-all -s FRE_AD001 finished without error at Thu Jan 26 20:48:17 MST 2017
done

==> output_FRE_AD002.txt <==
Started at Thu Jan 26 10:34:48 MST 2017 
Ended   at Thu Jan 26 20:55:23 MST 2017
#@#%# recon-all-run-time-hours 10.343
recon-all -s FRE_AD002 finished without error at Thu Jan 26 20:55:23 MST 2017
done

==> output_FRE_AD003.txt <==
Started at Thu Jan 26 10:34:53 MST 2017 
Ended   at Thu Jan 26 20:22:02 MST 2017
#@#%# recon-all-run-time-hours 9.786
recon-all -s FRE_AD003 finished without error at Thu Jan 26 20:22:02 MST 2017
done

==> output_FRE_AD004.txt <==
Started at Thu Jan 26 10:34:59 MST 2017 
Ended   at Thu Jan 26 20:39:40 MST 2017
#@#%# recon-all-run-time-hours 10.078
recon-all -s FRE_AD004 finished without error at Thu Jan 26 20:39:40 MST 2017
done

==> output_FRE_AD005.txt <==
Started at Thu Jan 26 10:35:24 MST 2017 
Ended   at Thu Jan 26 20:57:10 MST 2017
#@#%# recon-all-run-time-hours 10.363
recon-all -s FRE_AD005 finished without error at Thu Jan 26 20:57:10 MST 2017
done

==> output_FRE_AD006.txt <==
Started at Thu Jan 26 10:35:24 MST 2017 
Ended   at Thu Jan 26 20:06:51 MST 2017
#@#%# recon-all-run-time-hours 9.524
recon-all -s FRE_AD006 finished without error at Thu Jan 26 20:06:52 MST 2017
done

==> output_FRE_AD007.txt <==
Started at Thu Jan 26 10:35:24 MST 2017 
Ended   at Thu Jan 26 21:21:16 MST 2017
#@#%# recon-all-run-time-hours 10.764
recon-all -s FRE_AD007 finished without error at Thu Jan 26 21:21:16 MST 2017
done

==> output_FRE_AD008.txt <==
Started at Thu Jan 26 10:35:24 MST 2017 
Ended   at Thu Jan 26 21:37:38 MST 2017
#@#%# recon-all-run-time-hours 11.037
recon-all -s FRE_AD008 finished without error at Thu Jan 26 21:37:38 MST 2017
done

==> output_FRE_AD009.txt <==
Started at Thu Jan 26 10:35:24 MST 2017 
Ended   at Thu Jan 26 18:37:04 MST 2017
#@#%# recon-all-run-time-hours 8.028
recon-all -s FRE_AD009 finished without error at Thu Jan 26 18:37:04 MST 2017
done

==> output_FRE_AD010.txt <==
Started at Thu Jan 26 10:35:24 MST 2017 
Ended   at Thu Jan 26 20:27:54 MST 2017
#@#%# recon-all-run-time-hours 9.875
recon-all -s FRE_AD010 finished without error at Thu Jan 26 20:27:54 MST 2017
done

==> output_FRE_HC001.txt <==
Started at Thu Jan 26 10:35:24 MST 2017 
Ended   at Thu Jan 26 20:05:17 MST 2017
#@#%# recon-all-run-time-hours 9.498
recon-all -s FRE_HC001 finished without error at Thu Jan 26 20:05:17 MST 2017
done

==> output_FRE_HC002.txt <==
Started at Thu Jan 26 10:35:24 MST 2017 
Ended   at Thu Jan 26 19:24:21 MST 2017
#@#%# recon-all-run-time-hours 8.816
recon-all -s FRE_HC002 finished without error at Thu Jan 26 19:24:21 MST 2017
done

==> output_FRE_HC003.txt <==
Started at Thu Jan 26 10:35:24 MST 2017 
Ended   at Thu Jan 26 20:24:59 MST 2017
#@#%# recon-all-run-time-hours 9.826
recon-all -s FRE_HC003 finished without error at Thu Jan 26 20:24:59 MST 2017
done

==> output_FRE_HC004.txt <==
Started at Thu Jan 26 10:35:24 MST 2017 
Ended   at Thu Jan 26 22:11:03 MST 2017
#@#%# recon-all-run-time-hours 11.594
recon-all -s FRE_HC004 finished without error at Thu Jan 26 22:11:03 MST 2017
done

==> output_FRE_HC005.txt <==
Started at Thu Jan 26 10:35:24 MST 2017 
Ended   at Thu Jan 26 19:51:31 MST 2017
#@#%# recon-all-run-time-hours 9.269
recon-all -s FRE_HC005 finished without error at Thu Jan 26 19:51:31 MST 2017
done

==> output_FRE_HC006.txt <==
Started at Thu Jan 26 10:35:24 MST 2017 
Ended   at Thu Jan 26 22:28:17 MST 2017
#@#%# recon-all-run-time-hours 11.881
recon-all -s FRE_HC006 finished without error at Thu Jan 26 22:28:17 MST 2017
done

==> output_FRE_HC007.txt <==
Started at Thu Jan 26 10:35:24 MST 2017 
Ended   at Thu Jan 26 19:19:21 MST 2017
#@#%# recon-all-run-time-hours 8.733
recon-all -s FRE_HC007 finished without error at Thu Jan 26 19:19:21 MST 2017
done

==> output_FRE_HC008.txt <==
Started at Thu Jan 26 10:35:25 MST 2017 
Ended   at Thu Jan 26 19:44:30 MST 2017
#@#%# recon-all-run-time-hours 9.151
recon-all -s FRE_HC008 finished without error at Thu Jan 26 19:44:31 MST 2017
done

==> output_FRE_HC009.txt <==
Started at Thu Jan 26 10:35:24 MST 2017 
Ended   at Thu Jan 26 20:26:29 MST 2017
#@#%# recon-all-run-time-hours 9.851
recon-all -s FRE_HC009 finished without error at Thu Jan 26 20:26:29 MST 2017
done

==> output_FRE_HC010.txt <==
Started at Thu Jan 26 10:35:24 MST 2017 
Ended   at Thu Jan 26 18:17:06 MST 2017
#@#%# recon-all-run-time-hours 7.695
recon-all -s FRE_HC010 finished without error at Thu Jan 26 18:17:06 MST 2017
done

==> output_FRE_HC011.txt <==
Started at Thu Jan 26 10:35:27 MST 2017 
Ended   at Thu Jan 26 20:22:26 MST 2017
#@#%# recon-all-run-time-hours 9.783
recon-all -s FRE_HC011 finished without error at Thu Jan 26 20:22:27 MST 2017
done

==> output_FRE_HC012.txt <==
Started at Thu Jan 26 10:35:31 MST 2017 
Ended   at Thu Jan 26 19:46:44 MST 2017
#@#%# recon-all-run-time-hours 9.187
recon-all -s FRE_HC012 finished without error at Thu Jan 26 19:46:45 MST 2017
done

==> output_FRE_HC013.txt <==
Started at Thu Jan 26 10:35:37 MST 2017 
Ended   at Thu Jan 26 23:01:45 MST 2017
#@#%# recon-all-run-time-hours 12.436
recon-all -s FRE_HC013 finished without error at Thu Jan 26 23:01:45 MST 2017
done

==> output_FRE_HC014.txt <==
Started at Thu Jan 26 10:35:55 MST 2017 
Ended   at Thu Jan 26 21:09:40 MST 2017
#@#%# recon-all-run-time-hours 10.562
recon-all -s FRE_HC014 finished without error at Thu Jan 26 21:09:40 MST 2017
done

==> output_FRE_HC015.txt <==
Started at Thu Jan 26 10:36:24 MST 2017 
Ended   at Thu Jan 26 22:02:56 MST 2017
#@#%# recon-all-run-time-hours 11.442
recon-all -s FRE_HC015 finished without error at Thu Jan 26 22:02:56 MST 2017
done

==> output_FRE_HC016.txt <==
Started at Thu Jan 26 10:36:24 MST 2017 
Ended   at Thu Jan 26 19:24:34 MST 2017
#@#%# recon-all-run-time-hours 8.803
recon-all -s FRE_HC016 finished without error at Thu Jan 26 19:24:34 MST 2017
done
{% endhighlight %}

We will graph the hippocampal subfield results using R:

{% highlight bash %}
# Make a directory to organize the data and graphs
mkdir -p ~/compute/analyses/EDSD/HPC

# Run FreeSurfer script that combines the data from all participants
quantifyHippocampalSubfields.sh T1 ~/compute/analyses/EDSD/HPC/volumes.txt ~/compute/analyses/EDSD/FreeSurfer/

# Set the R environmental variables
module load r

# Run R
R
{% endhighlight %}

When R is loaded, run the following code:

{% highlight r %}
# Import data
mydata=read.table('~/compute/analyses/EDSD/HPC/volumes.txt',header=T,sep=" ")

# Create a groups variable
mydata$group=ifelse(grepl("FRE_AD???",mydata$Subject),"AD","HC")

# Set the output path for your PDF
pdf("~/compute/analyses/EDSD/HPC/boxplot.pdf")

# Add padding around your graph
par(oma=c(2,2,2,2))

# For loop across all your data columns
for (i in 2:(length(mydata)-1)){

# Calculate the p-value
sig=round(t.test(mydata[[i]]~mydata$group)$p.value,6)

# Generate a box plot
boxplot(mydata[[i]]~mydata$group, 
	data=mydata, 
	col=(c("deeppink","cyan")),
  main=colnames(mydata[i]),
  xlab=paste("p = ",sig,sep=""),
  ylab=expression(paste("Volume in ",mm^3,sep="")))
}

# Save PDF file
dev.off()
{% endhighlight %}

Once you've generated your PDF, open a new terminal window and copy the PDF to your local computer:

{% highlight bash %}
scp intj5@ssh.fsl.byu.edu:~/compute/analyses/EDSD/HPC/boxplot.pdf ~/Desktop/
{% endhighlight %}

