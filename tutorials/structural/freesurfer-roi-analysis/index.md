---
layout: tutorials
title: FreeSurfer | ROI Analysis
author: naomi
comments: true
date: 2016-08-15
---

## Objectives

After you complete this section you should be able to:

1. Inspect images processed by FreeSurfer and know how to identify common errors and inaccuracies
2. Utilize the various FreeSurfer tools and procedures to correct errors
3. Export segmentation and parcellation statistics on FreeSurfer Regions-of-Interest for use in statistical packages

## Before You Begin

*IMPORTANT* FreeSurfer’s viewing and editing software must be installed on the local machine you are using with either a Linux or Mac operating system; it cannot be used while logged onto the FSL.

## Copying Subjects from the FSL to Your Local Machine

From your terminal window you will need to run a command that copies files of interest in a secure manner. The ‘scp’ (secure copy) or ‘rsync’ Unix commands are ideal for these situations.

{% highlight bash %}
scp -r \
<username>@ssh.fsl.byu.edu:~/compute/analyses/class/FreeSurfer/<subjid> \
~/Desktop
{% endhighlight %}

## Identifying Errors

Make sure to source, or start-up, FreeSurfer in your terminal before you begin.

Errors occur when recon-all finishes but the white or pial surfaces (or less frequently, the aseg) are inaccurate. When the surfaces are inaccurate, you have to manually change the erroneous information in the file and then regenerate the surface. It is not possible to directly edit the location of a surface.

To begin checking the recon for accuracy, first ask yourself these two questions:

*	Do the surfaces follow gray matter/white matter borders?
*	Does the subcortical segmentation follow intensity boundaries?

If the answer to either is 'no', you will have to manually edit your subject.

There are five main types of errors/recon-all failures, and four main ways to fix these errors. The types of errors are:

1. Skull Strip Errors
2. Segmentation Errors
3. Intensity Normalization Error
4. Pial Surface misplacement
5. Topological Defect

And ways to fix these errors:

1. Erase voxels
2. Fill voxels
3. Clone voxels (i.e., copy from one volume to another)
4. Add “Control Points”

Remember to think about the image in 3 dimensions and not just a single slice. To aid in this mindset when editing view your subject from multiple orientations (e.g., coronal, axial, sagittal).

## Fixing Errors

Some subjects will need substantial editing while others may not. To ensure accuracy in your data, all subjects should be reviewed thoroughly; however, for the purpose of this tutorial you only need to pick 2 of the ones you processed to review.

To load a subject:

{% highlight bash %}
freeview -v <subjectname>/mri/T1.mgz \
<subjectname>/mri/brainmask.mgz \
-f <subjectname>/surf/lh.white:edgecolor=yellow \
<subjectname>/surf/lh.pial:edgecolor=red \
<subjectname>/surf/rh.white:edgecolor=yellow \
<subjectname>/surf/rh.pial:edgecolor=red
{% endhighlight %}

Skim through each subject in all 3 planes to ensure accuracy. To correct errors follow guidelines for each below, you may or may not need to do all or any of these for each subject:

1. Skull Strip Errors - https://surfer.nmr.mgh.harvard.edu/fswiki/FsTutorial/SkullStripFix_freeview
2. Segmentation (wm) Errors - https://surfer.nmr.mgh.harvard.edu/fswiki/FsTutorial/WhiteMatterEdits_freeview
3. Intensity Normalization Errors - https://surfer.nmr.mgh.harvard.edu/fswiki/FsTutorial/ControlPoints_freeview
4. Pial Surface misplacement - https://surfer.nmr.mgh.harvard.edu/fswiki/FsTutorial/PialEdits_freeview
5. Topological Defect - https://surfer.nmr.mgh.harvard.edu/fswiki/FsTutorial/TopologicalDefect_freeview
6. Talairach Errors (uncommon) - https://surfer.nmr.mgh.harvard.edu/fswiki/FsTutorial/Talairach_freeview

## Region of Interest (ROI) Analysis

Recall that statistics for default parcellation schemes are automatically generated as part of the FreeSurfer processing stream. You can find these files in the ‘stats’ directory under each subject’s FS directory:

| []() | []() | []() |
|---|---|---|
| subjid --> | stats --> | lh.aparc.stats |
| | | rh.aparc.stats  |
| | | lh.aparc.a2009s.stats  |
| | | rh.aparc.a2009s.stats  |
| | | etc. |

To become familiar with these stats files, locate one for one of your subjects and open it in a text viewer (e.g., vi, TextWrangler, etc.). Notice that the headers for some columns do not exactly align with the remaining column (see below), this is to facilitate the space-separated nature of the file for exporting, but is not very useful as a ‘human-readable’ file. Of all the values these files give you, only a few are particularly meaningful for study. For the aparc.stats ones, these include: StructName (name of the structure), SurfArea (surface area in mm2), GrayVol (cortical gray matter volume in mm3), and ThickAvg (average cortical thickness in mm).

| StructName              | NumVert | SurfArea | GrayVol | ThickAvg | ThickStd | MeanCurv | GausCurv | FoldInd | CurvInd |
|-------------------------|---------|----------|---------|----------|----------|----------|----------|---------|---------|
| bankssts                | 1053    | 745      | 1253    | 1.654    | 0.434    | 0.122    | 0.034    | 10      | 1.3     |
| caudalanteriorcingulate | 993     | 686      | 1885    | 2.547    | 0.593    | 0.171    | 0.053    | 29      | 2.2     |
| caudalmiddlefrontal     | 2940    | 1992     | 4907    | 2.245    | 0.553    | 0.113    | 0.029    | 24      | 3.6     |
| cuneus                  | 2745    | 1759     | 3583    | 1.893    | 0.439    | 0.158    | 0.059    | 47      | 6.7     |
| entorhinal              | 747     | 486      | 2168    | 3.259    | 0.89     | 0.114    | 0.036    | 7       | 1       |
| fusiform                | 3984    | 2704     | 8296    | 2.649    | 0.669    | 0.148    | 0.049    | 66      | 8.1     |
| inferiorparietal        | 6082    | 4057     | 8907    | 1.955    | 0.511    | 0.139    | 0.048    | 93      | 12      |
| inferiortemporal        | 4428    | 2872     | 7118    | 2.152    | 0.689    | 0.152    | 0.088    | 118     | 14.2    |
| isthmuscingulate        | 1539    | 969      | 2324    | 2.338    | 0.707    | 0.145    | 0.051    | 29      | 3.1     |
| lateraloccipital        | 7599    | 4976     | 10972   | 1.936    | 0.544    | 0.151    | 0.051    | 121     | 15.3    |
| lateralorbitofrontal    | 4335    | 2742     | 6790    | 2.286    | 0.574    | 0.148    | 0.067    | 101     | 12.9    |
| lingual                 | 5231    | 3372     | 7507    | 2.045    | 0.607    | 0.15     | 0.054    | 83      | 11.8    |

Exporting these values into a single text file for group analysis can be accomplished via several FS scripts: asegstats2table, aparcstats2table. While useful, these commands are not able to export all measurements (e.g., volume, surface area, thickness) for each structure from both hemispheres in a single process; multiple instances of the command must be run with different flags, then the files combined ‘by hand.’ To exercise your Unix skills and improve your familiarity with ‘for’ loops, below are some scripting commands you can use to create the desired output file with all of the data you desire.

Create a text file named ‘subjids.txt’ that contains the names of your subjects, with each subject name on a single line. For example:

1310
1315
1319
1320
1326
etc...

You can type this out ‘by hand’ easily since there are only a few subjects, but what if you have hundreds of subjects? Here is a quick command line trick to get an id file quickly.

{% highlight bash %}
cd <FSsubjectdirectory>
/bin/ls -1d * > subjid.txt
{% endhighlight %}

What this does is take the output of an ```ls``` command and directs it to a text file. Using ```/bin/ls``` strips the command of any special options that may be present in your .bashrc file, which could mess with the text formatting. The flag ```-1d``` prints out only directories (```d```) and puts them on a single line (```1```). The wildcard (```*```) means it’ll list everything in the folder to the text file because it matches any number of characters in a filename, including none. A smarter way to do this is use the ```?``` wildcard, which matches only a single character. For example, the command ```ls ?????``` will only list out matches that are 5 characters in width, whereas the command ```ls ????????``` will only list out matches 8 characters in width. Thus, the smart way to create our subject id file in the command line is to run:

{% highlight bash %}
/bin/ls -1d ???? > subjid.txt
{% endhighlight %}

This works because it will only return to us names that are 4 characters wide, which is the format of our subject ids (e.g., 1310). If our subject ids were both 4 and 6 characters wide (e.g., 1310, 061413), then this wouldn’t work in one step, we would have to do it twice (one for ???? and one for ??????), then combine the files.

Once you have your subjid.txt file completed, the following script can be used to generate a .csv file that contains data for all of the default FS cortical ROIs:

{% highlight bash %}
# Set variables
cd <yourFSdirectory>
SUBJECTS_DIR=<yourFSdirectory>
ids=( `cat subjectids.txt` )
outfile=$SUBJECTS_DIR/mris_aparc.stats.csv
label=( `cat ${ids[0]}/stats/lh.aparc.stats | sed -n '/^#/ !p' | egrep -o [a-z]\+` )

# Create outfile headers
echo -n "SubjID," > $outfile
for roi in ${label[@]}; do
    for hemi in lh rh; do
        for measure in sa vol thk; do
	    echo -n "${hemi}_${roi}_${measure}," >> $outfile
        done
    done
  done

# Echo out data for each subject into new datafile
for subj in ${ids[@]}; do
    echo -ne "\n$subj" >> $outfile
    for roi in ${label[@]}; do
        for hemi in lh rh; do
            stats=( `cat $subj/stats/$hemi.aparc.stats | egrep ^"${roi} " | sed -n '$ p'` )
            echo -n ,${stats[2]},${stats[3]},${stats[4]} >> $outfile
        done
    done
done
{% endhighlight %}
