---
layout: tutorials
title: FreeSurfer | ROI Analysis
author: naomi
comments: true
date: 2016-08-15
---

## Objectives

After you complete this section, you should be able to:

1. Perform full cortical reconstruction, parcellation, and labeling using FreeSurfer
2. View brain volumes in 2D: brain mask, white matter mask, and subcortical segmentation
3. View surfaces in 3D: pial, white and inflated surface; sulcal and curvature maps; thickness maps; cortical parcellation

export SUBJECTS_DIR=~/compute/analyses/class/FreeSurfer/
cd $SUBJECTS_DIR
pwd

asegstats2table --subjects 1304 1307 1310 1319 1326 2304 2310 2316 2320 2324 1306 1308 1315 1320 1327 2307 2314 2317 2323 2370 --tablefile aseg.vol.table

aparcstats2table --hemi lh --subjects 1304 1307 1310 1319 1326 2304 2310 2316 2320 2324 1306 1308 1315 1320 1327 2307 2314 2317 2323 2370 --tablefile lh.aparc.area.table

aparcstats2table --hemi rh --subjects 1304 1307 1310 1319 1326 2304 2310 2316 2320 2324 1306 1308 1315 1320 1327 2307 2314 2317 2323 2370 --tablefile rh.aparc.area.table

aparcstats2table --hemi lh --subjects 1304 1307 1310 1319 1326 2304 2310 2316 2320 2324 1306 1308 1315 1320 1327 2307 2314 2317 2323 2370 --meas thickness --parc aparc.a2009s --tablefile lh.aparc.a2009.thickness.table

aparcstats2table --hemi rh --subjects 1304 1307 1310 1319 1326 2304 2310 2316 2320 2324 1306 1308 1315 1320 1327 2307 2314 2317 2323 2370 --meas thickness --parc aparc.a2009s --tablefile rh.aparc.a2009.thickness.table
