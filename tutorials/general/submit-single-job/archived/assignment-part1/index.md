---
title: "Submitting Jobs on the Supercomputer"
author: "Assignment"
date: "Thursday, November 5, 2015"
output:
  slidy_presentation:
    css: ~/git/byu-f15/stylesheets/mystyle.css
    highlight: haddock
    incremental: no
    keep_md: yes
---

## Log Into Supercomputer

```bash
ssh <username>@ssh.fsl.byu.edu
```

## Create Script and Logfile Directories

```bash
mkdir -p ~/scripts/antsCorticalThickness
mkdir -p ~/logfiles/2015_11_05_1600
```

## Generate Test Batch Script

```bash
cd ~/scripts
vi testBatchScript.sh
```

Press `a` to enter into *insert* mode.

## testBatchScript.sh

```bash
#!/bin/bash
for subj in $(ls ~/compute/data/); do
sbatch \
-o ~/logfiles/2015_11_05_1600/output_${subj}.txt \
-e ~/logfiles/2015_11_05_1600/error_${subj}.txt \
~/scripts/testJobScript.sh \
${subj}
sleep 1
done
```

Press `esc` to enter into *command* mode. Type in `:wq` and press Return to save file.

## Positional Paramters

When you have a command line like this:

```
command object1 object2 object3
```

This is automagically parsed into positional variables:

```
${0}=command
${1}=object1
${2}=object2
${3}=object3
```

In the sbatch command previously, ignoring all the options (i.e., `-e`), `${subj}` becomes variable `${1}` in the JobScript.sh. Therefore you can use `${1}` anywhere in your JobScript.sh for when you want to use variable `${subj}`.

## Generate Test Job Script

```bash
vi testJobScript.sh
```

Press `a` to enter into *insert* mode.

## testJobScript.sh

```bash
#!/bin/bash
#SBATCH --time=50:00:00   # walltime
#SBATCH --ntasks=1   # number of processor cores (i.e. tasks)
#SBATCH --nodes=1   # number of nodes
#SBATCH --mem-per-cpu=16384M  # memory per CPU core

# INSERT CODE, AND RUN YOUR PROGRAMS HERE
SUBJ_DIR=~/compute/data/${1}
FILE=${SUBJ_DIR}/t1/n4.nii.gz
echo "Does this file exist" ${FILE}
if [ -e ${FILE} ]; then
echo "Yup"
else
echo "Nay"
fi
```

Press `esc` to enter into *command* mode. Type in `:wq` and press Return to save file.

## Submit testBatchScript.sh

By submitting the *testBatchScript.sh*, you will be looping through all your subject IDs and submitting a *testJobScript.sh* for each participant in your list (i.e., ls).

```bash
sh ~/scripts/testBatchScript.sh
```

## Read Output Files

```bash
cd ~/logfiles/2015_11_05_1600
tail output*.txt
```

## Read Error Files

```bash
cd ~/logfiles/2015_11_05_1600
tail error*.txt
```