---
title: "Submitting Jobs on the Supercomputer"
author: "Assignment"
date: "Monday, November 9, 2015"
output:
  slidy_presentation:
    css: ~/git/byu-f15/stylesheets/mystyle.css
    highlight: haddock
    incremental: no
    keep_md: yes
---

## Check for files

Log on to the supercomputer:

```bash
ssh <username>@ssh.fsl.byu.edu
```

Check to see if have these an *n4.nii.gz* file for all 20 participants:

```bash
find \
~/compute/data/ \
-type f \
-name "n4.nii.gz"
```

## Only if you are missing these files

```bash
rsync \
-rauv \
--exclude="DICOMs"
~/fsl_groups/fslg_byustudent/compute/data/ \
~/compute/data
```

## Sync over Template Files

```bash
rsync \
-rauv \
~/fsl_groups/fslg_byustudent/templates \
~/
```

## Create Script and Logfile Directories

```bash
mkdir -p ~/logfiles/2015_11_09_1300
```

## Generate Batch Script

```bash
cd ~/scripts/antsCorticalThickness
vi BatchScript.sh
```

Press `a` to enter into *insert* mode.

## BatchScript.sh

```bash
#!/bin/bash
for subj in $(ls ~/compute/data/); do
sbatch \
-o ~/logfiles/2015_11_09_1300/output_${subj}.txt \
-e ~/logfiles/2015_11_09_1300/error_${subj}.txt \
~/scripts/antsCorticalThickness/JobScript.sh \
${subj}
sleep 1
done
```

Press `esc` to enter into *command* mode. Type in `:wq` and press Return to save file.

## Generate Job Script

```bash
vi JobScript.sh
```

Press `a` to enter into *insert* mode.

## JobScript.sh

```bash
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

# Set the max number of threads to use for programs using OpenMP. Should be <= ppn. Does nothing if the program doesn't use OpenMP.
export OMP_NUM_THREADS=$SLURM_CPUS_ON_NODE

# LOAD MODULES AND ENVIRONMENTAL VARIABLES
export ANTSPATH=/fslhome/<username>/apps/ants/bin/
PATH=${ANTSPATH}:${PATH}

# INSERT CODE, AND RUN YOUR PROGRAMS HERE
SUBJ_DIR=/fslhome/<username>/compute/data/${1}
TEMPLATE_DIR=/fslhome/<username>/templates/NKI10andUnder
OUT_DIR=${SUBJ_DIR}/antsCorticalThickness
~/apps/ants/bin/antsCorticalThickness.sh -d 3 \
-a ${SUBJ_DIR}/t1/n4.nii.gz \
-e ${TEMPLATE_DIR}/T_template0.nii.gz \
-t ${TEMPLATE_DIR}/T_template0_BrainCerebellum.nii.gz \
-m ${TEMPLATE_DIR}/T_template0_BrainCerebellumProbabilityMask.nii.gz \
-f ${TEMPLATE_DIR}/T_template0_BrainCerebellumExtractionMask.nii.gz \
-p ${TEMPLATE_DIR}/Priors/priors%d.nii.gz \
-q 1 \
-o ${OUT_DIR}/
```

Press `esc` to enter into *command* mode. Type in `:wq` and press Return to save file.

## Submit BatchScript.sh

By submitting the *BatchScript.sh*, you will be looping through all your subject IDs and submitting a *JobScript.sh* for each participant in your list (i.e., ls).

```bash
sh ~/scripts/antsCorticalThickness/BatchScript.sh
```

## Read Output Files

```bash
cd ~/logfiles/2015_11_09_1300
tail output*.txt
```

## Read Error Files

```bash
cd ~/logfiles/2015_11_09_1300
tail error*.txt
```