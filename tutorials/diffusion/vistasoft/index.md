---
layout: tutorials
title: VistaSoft
author: naomi
comments: true
date: 2016-08-02
---

## Objectives

After you complete this section, you should be able to:

1. Describe tractography
2. Understand the difference between deterministic and probabilistic
3. Understand the difference between local versus global tractography
4. Fix the vistasoft code so it will run on the SuperComputer
5. Generate MATLAB function that can be called within a job script
6. Check Phase Encode direction and set the dwParams
7. Adjust dwParams based upon scanner typer

## Tractography

Another diffusion imaging method is a tractography-based approach as opposed to the voxel-based morphometry approach that TBSS employs. Tractography uses the principal water diffusion direction to reconstruct the trajectories of the fiber tracts. Tractography overcomes the alignment problems by estimating diffusion properties in native participant space. Because brain function is variable, brain shape is also variable and by measuring white matter integrity in native brain space you preserve individual differences. Tractography is considered the most accurate method for identifying white matter tracts in humans, but tractography has the limitation that it does not allow the whole brain to be investigated and, in some cases, requires some level of user interaction.

There are multiple tractography methods that can be used to determine the pathway between distant brain regions. When determining diffusion orientation at each voxel, there are two options: deterministic and probabilistic tractography. Deterministic (streamline) tractography models paths as a one-dimensional curve. Deterministic tractography uses the principal direction of diffusion to propagate trajectories from the defined anchor point until termination criterion are met (i.e., excessive angular deviation, minimum FA voxel threshold, and/or endpoint(s)). Probabilistic tractography generates a range of possible diffusion directions for each voxel instead of a single diffusion direction; therefore, probabilistic tractography actually models the uncertainty along the tract.

Tractography, whether you are using deterministic or probabilistic methods, can be completed either locally or globally. Local tractography reconstructs the path step-by-step using just the local orientation at each voxel. Local tractography is best suited for exploratory studies of brain connectivity. Tracts are reconstructed starting from a single seed region and are not constrained to any given target region, therefore, endpoints can occur anywhere within the brain. On the other hand, global tractography fits the entire path at once, using diffusion orientation of all voxels along the path length. Global tractography is best suited for reconstructing known white-matter pathways. Tracts are constrained to connections of two specific end points and symmetric between beginning seed and end target regions.

## Fix Code

Unfortunately, the VistaSoft code is riddled with problems. Because the lab that originally has written the code is still running MATLAB r2012, newer versions or even different versions of MATLAB are going to have issues. Here are the changes you will need to make to your code in order for it to run correctly on the Supercomputer:

{% highlight bash %}
vi

{% endhighlight %}

Starting on line 34 add the new code below the stored transformation file code:

{% highlight matlab %}
% Load the stored transformation file.
ec = load(ecXformFile);
t = vertcat(ec.xform(:).ecParams);

% NEW CODE (NJH 2016-09-22) Generate file that contains
% the motion correction for each volume
mc = t(:,1:6);
mc(:,4:6) = (mc(:,4:6)/(2*pi)\*360);
mc = dataset({mc 'x' 'y' 'z' 'pitch' 'roll' 'yaw'});
{% endhighlight %}

Shortly below that add the code to save the new datafile:

{% highlight matlab %}
% Save out a PNG figure with the same filename as the Eddy Currents correction xform.
[p,f,~] = fileparts(ecXformFile);
figurename = fullfile(p,[f,'.png']);

% NEW CODE (NJH 2016-09-22) Add export csv file for motion correction
filename = fullfile(p,[f,'.cxv']);
export(mc,'File',filename,'Delimiter',',');
{% endhighlight %}

Finally, we need to comment out some bad code and insert new code that works:

{% highlight matlab %}
% ORIGINAL CODE% printCommand = sprintf('print(%s, ''-painters'',''-dpng'', ''-noui'', ''%s'')', num2str(fh),figurename);
% NEW CODE (NJH 2016-09-22)
printCommand = sprintf('saveas(fh,figurename)');
{% endhighlight %}

## VistaSoft

Diffusion weighted images are preprocessed using the dtiInit preprocessing pipeline wrapper from Stanford open-source VISTASOFT package version 1.0 [https://github.com/vistalab/vistasoft](https://github.com/vistalab/vistasoft).

First, the skull-stripped T1-weighted images need to be reformatted to be compatible with the current pipeline. But where are we going to get skull-stripped T1-weighted images? If you completed antsCorticalThickness.sh or antsBrainExtraction.sh, then you could get your brain image that way. However, you should have run FreeSurfer on this dataset and you can grab the brain image from that pipeline as well. The FreeSurfer brain image may not be as accurate as ANTs, but it will suffice:

{% highlight bash %}
for subj in $(ls ~/compute/images/EDSD); do
mri_convert \
~/compute/analyses/EDSD/FreeSurfer/${subj}/mri/brain.mgz \
~/compute/images/EDSD/${subj}/t1/brain.nii.gz
done
{% endhighlight %}

To make the whole process of submitting jobs on the supercomputer even more confusing, you will submit a batch script, which will automatically submit a job script for each participant, and the job script will automatically run a MATLAB script to process the data. We will need to create a batch, job, and MATLAB function scripts.

### Batch Script

The batch script will use the for loop to loop through all the participant's under ~/compute/images/EDSD and submit the job script with the command line variable **subj**:

{% highlight bash %}
vi ~/scripts/EDSD/dtiInit_batch.sh
{% endhighlight %}

Copy and paste:

{% highlight bash %}
#!/bin/bash
for subj in $(ls ~/compute/images/EDSD/); do
sbatch \
-o ~/logfiles/${1}/output_${subj}.txt \
-e ~/logfiles/${1}/error_${subj}.txt \
~/scripts/EDSD/dtiInit_job.sh \
${subj}
sleep 1
done
{% endhighlight %}

### Job Script

The job script simply submits the MATLAB function **subjID** with the participant ID as a command line variable:

{% highlight bash %}
vi ~/scripts/EDSD/dtiInit_job.sh
{% endhighlight %}

Copy and paste:

{% highlight bash %}
#!/bin/bash

#SBATCH --time=04:00:00   # walltime
#SBATCH --ntasks=1  # number of processor cores (i.e. tasks)
#SBATCH --nodes=1   # number of nodes
#SBATCH --mem-per-cpu=24576M   # memory per CPU core

# Compatibility variables for PBS. Delete if not needed.
export PBS_NODEFILE=`/fslapps/fslutils/generate_pbs_nodefile`
export PBS_JOBID=$SLURM_JOB_ID
export PBS_O_WORKDIR="$SLURM_SUBMIT_DIR"
export PBS_QUEUE=batch

# Set the max number of threads to use for programs using OpenMP. Should be <= ppn. Does nothing if the program doesn't use OpenMP.
export OMP_NUM_THREADS=$SLURM_CPUS_ON_NODE

# LOAD MODULES, INSERT CODE, AND RUN YOUR PROGRAMS HERE
cd ~/scripts/EDSD/
module load matlab/r2010a
matlab -nodisplay -nojvm -nosplash -r "subjID('$1')"
{% endhighlight %}

### MATLAB function

You can get the MATLAB template from the shared directory:

{% highlight bash %}
cp -v ~/fsl_groups/fslg_byustudent/compute/matlab.nii.gz ~/templates/
{% endhighlight %}

Create your MATLAB function. The reason we are creating a function versus a script, is so we can pass a variable, namely the participant ID, into the script:

{% highlight bash %}
vi ~/scripts/EDSD/subjID.m
{% endhighlight %}

Copy and paste the following into your function. You do need all those percent signs at the beginning of the function:

{% highlight matlab %}
%%%%%%%
function subjID(x)

% Display participant ID:
display(x);

% Get home directory:
var = getenv('HOME');

% Add modules to MATLAB. Do not change the order of these programs:
SPM8Path = [var,'/apps/matlab/spm8'];
addpath(genpath(SPM8Path));
vistaPath = [var,'/apps/matlab/vistasoft'];
addpath(genpath(vistaPath));
AFQPath = [var,'/apps/matlab/AFQ'];
addpath(genpath(AFQPath));

% Set file names:
subjDir= [var,'/compute/images/EDSD/',x];
brainFile = [subjDir,'/t1/brain.nii.gz'];
t1File = [subjDir,'/t1/matlab.nii.gz'];
dtiFile = [subjDir,'/raw/dti.nii.gz'];
cd (subjDir);

% Move brain only image into the correct MATLAB FOV box:
mrAnatAverageAcpcNifti(brainFile,t1File,[var,'/templates/matlab.nii.gz']);

% Don't change the following code:
ni = readFileNifti(t1File);
ni = niftiSetQto(ni,ni.sto_xyz);
writeFileNifti(ni,t1File);

% Don't change the following code:
ni=readFileNifti(dtiFile);
ni=niftiSetQto(ni,ni.sto_xyz);
writeFileNifti(ni,dtiFile);

% Determine phase encode dir:
% > info=dicominfo([var,'/compute/images/EDSD/FRE_AD001/DICOM/diff/MR.22533.01274.dcm']);
% To get the manufacturer information:
% > info.(dicomlookup('0008','0070'))
% To get the axis of phase encoding with respect to the image:
% > info.(dicomlookup('0018','1312'))
% If phase encode dir is 'COL', then set 'phaseEncodeDir' to '2'
% If phase encode dir is 'ROW', then set 'phaseEncodeDir' to '1'
% For Siemens / Philips specific code we need to add 'rotateBvecsWithCanXform',
% AND ALWAYS DOUBLE CHECK phaseEncodeDir:
% > dwParams = dtiInitParams('rotateBvecsWithCanXform',1,'phaseEncodeDir',2,'clobber',1);
% For GE specific code,
% AND ALWAYS DOUBLE CHECK phaseEncodeDir:
% > dwParams = dtiInitParams('phaseEncodeDir',2,'clobber',1);
dwParams = dtiInitParams('rotateBvecsWithCanXform',1,'phaseEncodeDir',2,'clobber',1);

% Here's the one line of code to do the DTI preprocessing:
dtiInit(dtiFile, t1File, dwParams);

% Clean up files and exit:
movefile('dti_*','raw/');
movefile('dtiInitLog.mat','raw/');
exit;
{% endhighlight %}

You should not have to change any of this code for the neuroimaging class; however, if you are processing your own dataset, you need to double check the manufacturer and phaseEncodeDir and change those within the dwParams command.

### Submit Batch Script

Finally, submit the whole process by submitting the batch script:

{% highlight bash %}
var=`date +"%Y%m%d-%H%M%S"`
mkdir -p ~/logfiles/${var}
sh ~/scripts/EDSD/dtiInit_batch.sh $var
{% endhighlight %}
