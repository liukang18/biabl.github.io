---
title: "Morphometry"
author: "Assignment"
date: "Monday, November 16, 2015"
output:
  slidy_presentation:
    css: ~/git/byu-f15/stylesheets/mystyle.css
    highlight: haddock
    incremental: yes
    keep_md: yes
---

## Log Into the Supercomputer

```bash
ssh <username>@ssh.fsl.byu.edu
```

Make directory for potential analyses

```bash
mkdir -p ~/compute/projects/corticalthickness/data
mkdir -p ~/compute/projects/tbm/data
mkdir -p ~/compute/projects/vbm/data
```

## Sync Template Directory

```bash
rsync \
-rauv \
~/fsl_groups/fslg_byustudent/templates/ \
~/templates
```

## Copy Files and Add Subject ID to Filename

Copy cortical thickness normalized to template images:

```bash
for subj in $(ls ~/compute/data); do
SUBJ_DIR=~/compute/data/${subj}/antsCorticalThickness/
cp -v \
${SUBJ_DIR}/CorticalThicknessNormalizedToTemplate.nii.gz \
~/compute/projects/corticalthickness/data/${subj}.nii.gz
done
```

Copy log Jacobian images:

```bash
for subj in $(ls ~/compute/data); do
SUBJ_DIR=~/compute/data/${subj}/antsCorticalThickness/
cp -v \
${SUBJ_DIR}/SubjectToTemplateLogJacobian.nii.gz \
~/compute/projects/tbm/data/${subj}.nii.gz
done
```

## Generate Normalized GM Images

```bash
for subj in $(ls ~/compute/data); do
SUBJ_DIR=~/compute/data/${subj}/antsCorticalThickness
~/apps/c3d/bin/c3d \
${SUBJ_DIR}/BrainNormalizedToTemplate.nii.gz \
~/templates/NKI10andUnder/T_template0_GMMask.nii.gz \
-multiply \
-o ${SUBJ_DIR}/GMSegmented.nii.gz
done
```

## Modulate GM Images

```bash
for subj in $(ls ~/compute/data); do
SUBJ_DIR=~/compute/data/${subj}/antsCorticalThickness
~/apps/c3d/bin/c3d \
${SUBJ_DIR}/GMSegmented.nii.gz \
${SUBJ_DIR}/SubjectToTemplateLogJacobian.nii.gz \
-multiply \
-o ${SUBJ_DIR}/GMModulated.nii.gz
done
```

## Copy Images

```bash
for subj in $(ls ~/compute/data); do
cp -v \
~/compute/data/${subj}/antsCorticalThickness/GMModulated.nii.gz \
~/compute/projects/vbm/data/${subj}.nii.gz
done
```