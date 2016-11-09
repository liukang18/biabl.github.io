---
layout: tutorials
title: Extracting Tables
author: naomi
comments: true
date: 2016-11-09
---

## AFQ

<div class="embed-container">
<iframe src="https://player.vimeo.com/video/190931148?byline=0&portrait=0" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>
</div>
<br>

This section of the tutorial will teach you how to extract values on anisotropy and diffusivity measures for the white-matter pathways reconstructed by AFQ.

{% highlight bash %}
mkdir -p ~/compute/analyses/EDSD/AFQ/stats
mkdir -p ~/compute/analyses/EDSD/AFQ-CC/stats
module load matlab
matlab
{% endhighlight %}

When MATLAB opens:

{% highlight matlab %}
var = getenv('HOME');
SPM8Path = [var, '/apps/matlab/spm8'];
addpath(genpath(SPM8Path));
vistaPath = [var, '/apps/matlab/vistasoft'];
addpath(genpath(vistaPath));
AFQPath = [var, '/apps/matlab/AFQ'];
addpath(genpath(AFQPath));
{% endhighlight %}

Use this code to save the data from the 20 fiber tracts:

{% highlight matlab %}
load ~/compute/analyses/EDSD/AFQ-CC/afq_cc_2016_09_30_1005.mat
fgname={'left_thalamic_radiation', 'right_thalamic_radiation', 'left_corticospinal', 'right_corticospinal', 'left_cingulum_cingulate', 'right_cingulum_cingulate', 'left_cingulum_hippocampus', 'right_cingulum_hippocampus', 'callosum_forceps_major', 'callosum_forceps_minor', 'left_ifof', 'right_ifof', 'left_ilf', 'right_ilf', 'left_slf', 'right_slf', 'left_uncinate', 'right_uncinate', 'left_arcuate', 'right_arcuate'};
outdir=fullfile('~/compute/analyses/EDSD/AFQ/stats/');
for n = 1:20
csvwrite(fullfile(outdir,strcat('ad_',fgname{n},'_fa.csv')),afq.patient_data(n).FA)
csvwrite(fullfile(outdir,strcat('ad_',fgname{n},'_rd.csv')),afq.patient_data(n).RD)
csvwrite(fullfile(outdir,strcat('ad_',fgname{n},'_md.csv')),afq.patient_data(n).MD)
csvwrite(fullfile(outdir,strcat('ad_',fgname{n},'_ad.csv')),afq.patient_data(n).AD)
csvwrite(fullfile(outdir,strcat('hc_',fgname{n},'_fa.csv')),afq.control_data(n).FA)
csvwrite(fullfile(outdir,strcat('hc_',fgname{n},'_rd.csv')),afq.control_data(n).RD)
csvwrite(fullfile(outdir,strcat('hc_',fgname{n},'_md.csv')),afq.control_data(n).MD)
csvwrite(fullfile(outdir,strcat('hc_',fgname{n},'_ad.csv')),afq.control_data(n).AD)
end
{% endhighlight %}

Use this code to save the corpus callosum segmentation data:

{% highlight matlab %}
fgname={'cc_occipital', 'cc_post_parietal', 'cc_sup_parietal', 'cc_motor', 'cc_sup_frontal', 'cc_ant_frontal', 'cc_orb_frontal', 'cc_temporal'};
outdir=fullfile('~/compute/analyses/EDSD/AFQ-CC/stats/');
for n = 21:28
csvwrite(fullfile(outdir,strcat('ad_',fgname{n-20},'_fa.csv')),afq.patient_data(n).FA)
csvwrite(fullfile(outdir,strcat('ad_',fgname{n-20},'_rd.csv')),afq.patient_data(n).RD)
csvwrite(fullfile(outdir,strcat('ad_',fgname{n-20},'_md.csv')),afq.patient_data(n).MD)
csvwrite(fullfile(outdir,strcat('ad_',fgname{n-20},'_ad.csv')),afq.patient_data(n).AD)
csvwrite(fullfile(outdir,strcat('hc_',fgname{n-20},'_fa.csv')),afq.control_data(n).FA)
csvwrite(fullfile(outdir,strcat('hc_',fgname{n-20},'_rd.csv')),afq.control_data(n).RD)
csvwrite(fullfile(outdir,strcat('hc_',fgname{n-20},'_md.csv')),afq.control_data(n).MD)
csvwrite(fullfile(outdir,strcat('hc_',fgname{n-20},'_ad.csv')),afq.control_data(n).AD)
end
{% endhighlight %}

## TRACULA

<div class="embed-container">
<iframe src="https://player.vimeo.com/video/190931150?byline=0&portrait=0" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>
</div>
<br>

This section of the tutorial will teach you how to extract values on anisotropy and diffusivity measures for the white-matter pathways reconstructed by TRACULA.

### Individual Tables

You can view the tract profile from any participant with the following code. Tract profiles are located under each participant directory within the **dpath** folder:

{% highlight bash %}
cat ~/Desktop/TRACULA/data/FRE_AD002/dpath/lh.ilf_AS_avg33_mni_bbr/pathstats.byvoxel.txt
{% endhighlight %}

The output will look something like this:

{% highlight bash %}
# Title Pathway Statistics
#
# generating_program /fslhome/intj5/apps/freesurfer/bin/dmri_pathstats
# cvs_version
# cmdline /fslhome/intj5/apps/freesurfer/bin/dmri_pathstats --intrc /fslhome/intj5/compute/analyses/EDSD/TRACULA/data//FRE_AD002/dpath/lh.ilf_AS_avg33_mni_bbr --dtbase /fslhome/intj5/compute/analyses/EDSD/TRACULA/data//FRE_AD002/dmri/dtifit --path lh.ilf --subj FRE_AD002 --out /fslhome/intj5/compute/analyses/EDSD/TRACULA/data//FRE_AD002/dpath/lh.ilf_AS_avg33_mni_bbr/pathstats.overall.txt --outvox /fslhome/intj5/compute/analyses/EDSD/TRACULA/data//FRE_AD002/dpath/lh.ilf_AS_avg33_mni_bbr/pathstats.byvoxel.txt
# sysname Linux
# hostname m8-20-9
# machine x86_64
# user intj5
# anatomy_type pathway
#
# subjectname FRE_AD002
# pathwayname lh.ilf
#
# pathway start
x y z AD RD MD FA AD_Avg RD_Avg MD_Avg FA_Avg
73 65 20 0.00115373 0.000613763 0.000793751 0.384316 0.000997438 0.000651291 0.000766674 0.270435
73 64 20 0.001088 0.000606004 0.000766669 0.360318 0.000997715 0.000642159 0.00076068 0.279503
73 63 20 0.00108034 0.000632546 0.00078181 0.324345 0.00100525 0.000640159 0.000761856 0.286917
74 62 20 0.00105986 0.000611806 0.000761156 0.332019 0.00100224 0.000648368 0.000766326 0.279549
74 61 20 0.000994137 0.000657125 0.000769462 0.249631 0.00099079 0.000668477 0.000775914 0.257465
74 60 20 0.000941754 0.000690851 0.000774486 0.217151 0.000982401 0.000683258 0.00078297 0.245268
75 59 21 0.000961816 0.000702919 0.000789218 0.208171 0.000968346 0.000664992 0.000766108 0.258156
75 58 21 0.00101139 0.000649977 0.000770446 0.305103 0.000970724 0.000634405 0.000746511 0.289821
75 57 21 0.00101643 0.000627333 0.000757032 0.336318 0.000993506 0.000624058 0.000747205 0.319832
75 56 21 0.00102268 0.000640603 0.000767962 0.324198 0.0010228 0.000626392 0.000758528 0.32915
76 55 21 0.00104812 0.000666155 0.000793478 0.318932 0.00103941 0.000634444 0.000769434 0.325696
76 54 21 0.000951794 0.000654712 0.000753739 0.309988 0.00101742 0.000632282 0.000760665 0.322584
76 53 21 0.0009554 0.000664028 0.000761152 0.29197 0.00100628 0.00062428 0.000751616 0.320337
76 52 21 0.00103748 0.000644417 0.000775437 0.332768 0.00100561 0.000595517 0.000732217 0.344596
76 51 21 0.00104478 0.000582389 0.000736518 0.384094 0.000999889 0.000565939 0.000710594 0.369947
76 50 21 0.00100713 0.000511433 0.000676666 0.415115 0.00101463 0.000550538 0.00070524 0.386185
76 49 21 0.000979359 0.000477957 0.000645091 0.42764 0.00102582 0.000523789 0.000691136 0.414693
76 48 21 0.000949206 0.000456455 0.000620705 0.43858 0.00105316 0.000500842 0.000684948 0.450366
76 47 21 0.00092323 0.000444374 0.000603992 0.43793 0.00107133 0.000480104 0.000677175 0.480792
76 46 21 0.000949573 0.000487183 0.000641313 0.407784 0.00108812 0.000483744 0.0006852 0.488206
76 45 21 0.000928382 0.000502028 0.000644146 0.410892 0.00108381 0.000516685 0.000705725 0.462779
76 44 21 0.000838108 0.000541013 0.000640045 0.376847 0.00104895 0.000556287 0.000720504 0.417266
76 43 21 0.000902896 0.00060422 0.000703779 0.305954 0.00103818 0.000594532 0.000742409 0.367576
75 42 21 0.000973572 0.000620866 0.000738434 0.279709 0.0010699 0.000614292 0.00076616 0.346224
75 41 21 0.000951228 0.000640935 0.000744366 0.245604 0.00107713 0.000613386 0.000767968 0.342025
75 40 21 0.000795767 0.000620059 0.000678628 0.159069 0.00105463 0.000606649 0.000755975 0.332908
75 39 21 0.000848627 0.000627297 0.000701074 0.215326 0.00106173 0.000605146 0.00075734 0.336693
75 38 21 0.000813577 0.000639754 0.000697695 0.205591 0.00106429 0.000591114 0.000748836 0.351237
74 37 22 0.000940891 0.000623158 0.000729069 0.258898 0.001043 0.000581795 0.000735529 0.35121
74 36 22 0.000919137 0.000606959 0.000711018 0.251792 0.00102981 0.000597411 0.000741545 0.33275
74 36 23 0.00100478 0.000627892 0.000753521 0.309435 0.00103822 0.000597945 0.000744704 0.338008
73 35 23 0.00104403 0.000626817 0.000765888 0.338078 0.0010552 0.000605131 0.000755156 0.339866
73 34 23 0.000869571 0.000591849 0.000684423 0.27114 0.00102502 0.000600027 0.00074169 0.328081
73 33 24 0.000891673 0.000555185 0.000667348 0.342385 0.00100676 0.000585179 0.000725705 0.332512
72 32 24 0.00106762 0.000538744 0.000715035 0.432505 0.00103736 0.00057391 0.000728393 0.363366
72 31 24 0.000917754 0.000550314 0.000672794 0.338932 0.00103946 0.000557826 0.000718371 0.378361
71 30 24 0.00103203 0.000531246 0.000698173 0.427369 0.00104502 0.000571747 0.000729506 0.367652
71 29 24 0.000904974 0.000581045 0.000689021 0.322243 0.00100594 0.00060492 0.000738594 0.323178
71 28 24 0.000943329 0.000575494 0.000698106 0.324091 0.000999999 0.000619588 0.000746391 0.30432
70 27 24 0.00104889 0.00058021 0.000736437 0.370328 0.00101415 0.000621494 0.000752379 0.307613
70 26 23 0.000946392 0.0006475 0.000747131 0.243816 0.000992144 0.000617112 0.000742124 0.298709
70 25 23 0.000858347 0.000638089 0.000711508 0.224469 0.000971588 0.00060373 0.00072635 0.298021
69 24 23 0.000799646 0.000687919 0.000725161 0.122466 0.00095822 0.000596165 0.000716849 0.296511
69 23 23 0.000799977 0.000676291 0.00071752 0.121311 0.000947239 0.000595767 0.000712924 0.290574
69 22 23 0.000752362 0.000642736 0.000679278 0.0983584 0.000943177 0.000598633 0.00071348 0.285253
69 21 22 0.000765309 0.000628512 0.000674111 0.12113 0.000943917 0.000605093 0.000718034 0.282133
68 20 22 0.000732658 0.000631063 0.000664928 0.103275 0.000968219 0.00060726 0.000727578 0.29634
68 19 22 0.000808548 0.000613076 0.000678233 0.182327 0.00097979 0.000607928 0.000731881 0.30275
# pathway end
{% endhighlight %}

This text file contains various diffusion measures, one row for each position along the trajectory of the path. The first three entries in each row are the x, y, z coordinates in native diffusion space. The next four entries are the axial diffusivity, radial diffusivity, mean diffusivity, and fractional anisotropy at that position on the maximum a posteriori path. The last four entries are the axial diffusivity, radial diffusivity, mean diffusivity, and fractional anisotropy at the same position, averaged over all sampled paths.

Points are always ordered in the following way:

* CST : Brainstem -> Motor cortex
* UNC : Temporal -> Orbitofrontal
* ILF : Temporal -> Occipital
* ATR : Frontal cortex -> Thalamus
* CCG : Posterior -> Anterior
* CAB : Posterior -> Anterior
* SLFP : Frontal -> Parietal
* SLFT : Frontal -> Temporal
* F-MAJOR/MINOR : Left -> Right

### Group Tables

First we need to create a new configuration file that contains all the participant information and not individual configuration files for each participant:

{% highlight bash %}
vi ~/compute/analyses/EDSD/TRACULA/dmrirc.config
{% endhighlight %}

Copy and paste the following information into the new configuration file:

{% highlight bash %}
setenv SUBJECTS_DIR /fslhome/intj5/compute/analyses/EDSD/FreeSurfer/

set dtroot = /fslhome/intj5/compute/analyses/EDSD/TRACULA/data/

set subjlist = ( FRE_AD001 \
FRE_AD002 \
FRE_AD003 \
FRE_AD004 \
FRE_AD005 \
FRE_AD006 \
FRE_AD007 \
FRE_AD008 \
FRE_AD009 \
FRE_AD010 \
FRE_HC001 \
FRE_HC002 \
FRE_HC003 \
FRE_HC004 \
FRE_HC005 \
FRE_HC006 \
FRE_HC007 \
FRE_HC008 \
FRE_HC009 \
FRE_HC010 \
FRE_HC011 \
FRE_HC012 \
FRE_HC013 \
FRE_HC014 \
FRE_HC015 \
FRE_HC016 )

set dcmroot = /fslhome/intj5/compute/images/EDSD/

set dcmlist = ( FRE_AD001/raw/dti.nii.gz \
FRE_AD002/raw/dti.nii.gz \
FRE_AD003/raw/dti.nii.gz \
FRE_AD004/raw/dti.nii.gz \
FRE_AD005/raw/dti.nii.gz \
FRE_AD006/raw/dti.nii.gz \
FRE_AD007/raw/dti.nii.gz \
FRE_AD008/raw/dti.nii.gz \
FRE_AD009/raw/dti.nii.gz \
FRE_AD010/raw/dti.nii.gz \
FRE_HC001/raw/dti.nii.gz \
FRE_HC002/raw/dti.nii.gz \
FRE_HC003/raw/dti.nii.gz \
FRE_HC004/raw/dti.nii.gz \
FRE_HC005/raw/dti.nii.gz \
FRE_HC006/raw/dti.nii.gz \
FRE_HC007/raw/dti.nii.gz \
FRE_HC008/raw/dti.nii.gz \
FRE_HC009/raw/dti.nii.gz \
FRE_HC010/raw/dti.nii.gz \
FRE_HC011/raw/dti.nii.gz \
FRE_HC012/raw/dti.nii.gz \
FRE_HC013/raw/dti.nii.gz \
FRE_HC014/raw/dti.nii.gz \
FRE_HC015/raw/dti.nii.gz \
FRE_HC016/raw/dti.nii.gz )

set bveclist = ( /fslhome/intj5/compute/images/EDSD/FRE_AD001/raw/dti.bvec \
/fslhome/intj5/compute/images/EDSD/FRE_AD002/raw/dti.bvec \
/fslhome/intj5/compute/images/EDSD/FRE_AD003/raw/dti.bvec \
/fslhome/intj5/compute/images/EDSD/FRE_AD004/raw/dti.bvec \
/fslhome/intj5/compute/images/EDSD/FRE_AD005/raw/dti.bvec \
/fslhome/intj5/compute/images/EDSD/FRE_AD006/raw/dti.bvec \
/fslhome/intj5/compute/images/EDSD/FRE_AD007/raw/dti.bvec \
/fslhome/intj5/compute/images/EDSD/FRE_AD008/raw/dti.bvec \
/fslhome/intj5/compute/images/EDSD/FRE_AD009/raw/dti.bvec \
/fslhome/intj5/compute/images/EDSD/FRE_AD010/raw/dti.bvec \
/fslhome/intj5/compute/images/EDSD/FRE_HC001/raw/dti.bvec \
/fslhome/intj5/compute/images/EDSD/FRE_HC002/raw/dti.bvec \
/fslhome/intj5/compute/images/EDSD/FRE_HC003/raw/dti.bvec \
/fslhome/intj5/compute/images/EDSD/FRE_HC004/raw/dti.bvec \
/fslhome/intj5/compute/images/EDSD/FRE_HC005/raw/dti.bvec \
/fslhome/intj5/compute/images/EDSD/FRE_HC006/raw/dti.bvec \
/fslhome/intj5/compute/images/EDSD/FRE_HC007/raw/dti.bvec \
/fslhome/intj5/compute/images/EDSD/FRE_HC008/raw/dti.bvec \
/fslhome/intj5/compute/images/EDSD/FRE_HC009/raw/dti.bvec \
/fslhome/intj5/compute/images/EDSD/FRE_HC010/raw/dti.bvec \
/fslhome/intj5/compute/images/EDSD/FRE_HC011/raw/dti.bvec \
/fslhome/intj5/compute/images/EDSD/FRE_HC012/raw/dti.bvec \
/fslhome/intj5/compute/images/EDSD/FRE_HC013/raw/dti.bvec \
/fslhome/intj5/compute/images/EDSD/FRE_HC014/raw/dti.bvec \
/fslhome/intj5/compute/images/EDSD/FRE_HC015/raw/dti.bvec \
/fslhome/intj5/compute/images/EDSD/FRE_HC016/raw/dti.bvec )

set bvalfile = /fslhome/intj5/compute/images/EDSD/FRE_AD002/raw/dti.bval
{% endhighlight %}

The pathstats.byvoxel.txt files will generally not contain the same number of positions (rows) for each participant because the tracts are reconstructed in each participant's native diffusion space and not in a template space. Thus they are not ready for performing group analyses yet. To combine these files from multiple participants, interpolating the anisotropy and diffusivity values at corresponding positions along the tract for all participants, run the following:

{% highlight bash %}
trac-all -stat -c ~/compute/analyses/EDSD/TRACULA/dmrirc.config
{% endhighlight %}

This will create a directory named **stats** under the main TRACULA output directory, **data**, and save one table per tract per diffusion measure. In these tables, each row is a different position along the trajectory of the tract and each column is a different participant.
