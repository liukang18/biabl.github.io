---
layout: tutorials
title: Plot and Render Fibers
author: naomi
comments: true
date: 2016-10-06
---

## Objectives

After you complete this section, you should be able to:

1. Plot Tract Profiles for groups with 90% confidence interval
2. Plot Tract Profiles for the control group with DTI scalar as a heatmap
3. Render individual tracts for each participant
4. Render individual tracts for each participant with DTI scalars as a Tract Profile
5. Generate group statistics and save the Tract Profile
6. Render group statistics

## Before You Begin

For most of the visualizations and analyses, the only file needed is the afq.mat file that also contains the corpus callosum segmentation:

{% highlight bash %}
rsync -rauv intj5@ssh.fsl.byu.edu:~/compute/analyses/EDSD/AFQ-CC/ ~/Desktop/
{% endhighlight %}

On your local computer, you need a working version of MATLAB and the following MATLAB packages: SPM 5, SPM 8, VistaSoft, and AFQ. You can follow the code here if you need to figure out how to install these additional packages:

{% highlight bash %}
cd ~/Applications/
git clone https://github.com/shmp0722/LHON2.git LHON2
git clone https://github.com/yeatmanlab/AFQ.git afq
git clone https://github.com/vistalab/vistasoft.git vistasoft
wget http://www.fil.ion.ucl.ac.uk/spm/download/restricted/contradistinction/spm5.zip
unzip spm5.zip && rm spm5.zip
wget http://www.fil.ion.ucl.ac.uk/spm/download/restricted/idyll/spm8.zip
unzip spm8.zip && rm spm8.zip
{% endhighlight %}

## Plots

When you run MATLAB, you'll always need to run the following code to set MATLAB's path directory. If you ever quit MATLAB, these variables will always need to be reset:

{% highlight matlab %}
% Get home directory:
var = getenv('HOME');

% Add modules to MATLAB. Do not change the order of these programs:
SPM8Path = [var,'/Applications/spm8'];
addpath(genpath(SPM8Path));
vistaPath = [var,'/Applications/vistasoft'];
addpath(genpath(vistaPath));
AFQPath = [var,'/Applications/afq'];
addpath(genpath(AFQPath));
LHON2Path = [var,'/Applications/LHON2'];
addpath(genpath(LHON2Path));
{% endhighlight %}

To load the afq.mat file type the following into MATLAB:

{% highlight matlab %}
load ~/Desktop/afq_cc_2016_09_30_1005.mat
{% endhighlight %}

### Group Averages

<img class="img-responsive" alt="" src="images/plot.png">

Tract profiles (DTI scalars along the tract) can be automatically plotted in MATLAB though clumsily. Long term you'll want develop your own code in R to graph the results. After loading in your afq.mat dataset, you can generate group average graphs for ALL 28 tracts:

{% highlight matlab %}
all = [1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28];
AFQ_plot('AD',afq.patient_data,...
'HC',afq.control_data,...
'tracts',all,...
'group',...
'property','fa');
{% endhighlight %}

If you just want to look at one specific tract type:

{% highlight matlab %}
AFQ_plot('AD',afq.patient_data,...
'HC',afq.control_data,...
'tracts',[1],...
'group',...
'property','fa');
{% endhighlight %}

If you want to change from FA to other DTI scalars, change the 'property' option to either: 'fa', 'rd', 'md', 'ad'.

### Control Group Heatmap

<img class="img-responsive" alt="" src="images/plot-heatmap.png">

You are also able to plot a heatmap of the control group. The gray lines are individual participants and the average of the color group is represented by the heatmap. The color changes with the y-axis, so in the following image as FA increases the heatmap gets redder and as FA decreases it goes bluer. This type of image is nice to pair with a 3D view of the tract later on.

{% highlight matlab %}
AFQ_plot(afq,'colormap','tracts',[3])
{% endhighlight %}

## Individual Renderings

To look at an individual participant, you need to load their fiber group data, their processed DTI data, and their T1 image. For now let us just download one participant and not the entire data set:

{% highlight bash %}
rsync -rauv --exclude="DICOM" intj5@ssh.fsl.byu.edu:~/compute/images/EDSD/FRE_AD001 ~/Desktop/
{% endhighlight %}

In MATLAB, load the participant's fiber group (fg), their dt6.mat file (dt) and a t1 image to use as a background to overlay fibers. Because we registered all the participants brains affinely to an MNI template, we can use the MNI template as a general background. This is more for visualization and not about precision:

{% highlight matlab %}
fg=dtiReadFibers(fullfile([var,'/Desktop/FRE_AD001/dti55trilin/fibers/MoriGroups_clean_D5_L4.mat']));
dt=dtiLoadDt6(fullfile([var,'/Desktop/FRE_AD001/dti55trilin/dt6.mat']));
t1 = readFileNifti([var,'/Applications/vistasoft/mrDiffusion/templates/MNI_JHU_FA.nii.gz']);
{% endhighlight %}

### Fiber Tracts

<img class="img-responsive" alt="" src="images/render-individual.png">

You can plot the fibers of a participant. There are a LOT of options under AFQ_RenderFibers, so explore the README file to learn more:

{% highlight matlab %}
AFQ_RenderFibers(fg(3),'numfibers',500,'color',[1 0 0],'subplot',[1 2 1]);
title(fg(3).name,'fontsize',18)
AFQ_RenderFibers(fg(4),'numfibers',500,'color',[1 0 0],'subplot',[1 2 2]);
title(fg(4).name,'fontsize',18)
{% endhighlight %}

### Heatmap

<img class="img-responsive" alt="" src="images/render-heatmap.png">

You can also look at the heatmap of an individual participant:

{% highlight matlab %}
crange = [.3 .6]; numfibers=200; radius = 5; subdivs = 100; cmap = 'jet'; newfig = 0;
Profile = SO_FiberValsInTractProfiles(fg(3),dt,'SI',100,1);
AFQ_RenderFibers(fg(3),'numfibers',500,'color',[.5 .5 .5],'alpha',0.5);
AFQ_RenderTractProfile(Profile.coords.acpc, radius, Profile.vals.fa, subdivs, cmap, crange, newfig);
AFQ_AddImageTo3dPlot(t1, [-5, 0, 0]);
{% endhighlight %}

## Group Renderings

In order to generate a tract profile you can load on top of an individual tract, we have to perform statistics.

### Tract Profile

First, run a t-test between groups:

{% highlight matlab %}
for jj = 1:20
    [hAD(jj,:),pAD(jj,:),~,TstatsAD(jj)] = ttest2(afq.patient_data(jj).AD,afq.control_data(jj).AD);
    [hFA(jj,:),pFA(jj,:),~,TstatsFA(jj)] = ttest2(afq.patient_data(jj).FA,afq.control_data(jj).FA);
    [hMD(jj,:),pMD(jj,:),~,TstatsMD(jj)] = ttest2(afq.patient_data(jj).MD,afq.control_data(jj).MD);
    [hRD(jj,:),pRD(jj,:),~,TstatsRD(jj)] = ttest2(afq.patient_data(jj).RD,afq.control_data(jj).RD);
end
{% endhighlight %}

Next, convert the results into a way that can be visually mapped on the tracts:

{% highlight matlab %}
numNodes = 100;
[fa, md, rd, ad, cl, volume, TractProfile] = AFQ_ComputeTractProperties(fg,dt,numNodes);
for jj = 1:20
    TractProfile(jj) = AFQ_TractProfileSet(TractProfile(jj),'vals','pADval',pAD(jj,:));
    TractProfile(jj) = AFQ_TractProfileSet(TractProfile(jj),'vals','pFAval',pFA(jj,:));
    TractProfile(jj) = AFQ_TractProfileSet(TractProfile(jj),'vals','pMDval',pMD(jj,:));
    TractProfile(jj) = AFQ_TractProfileSet(TractProfile(jj),'vals','pRDval',pRD(jj,:));
    TractProfile(jj) = AFQ_TractProfileSet(TractProfile(jj),'vals','TstatAD',TstatsAD(jj).tstat);
    TractProfile(jj) = AFQ_TractProfileSet(TractProfile(jj),'vals','TstatFA',TstatsFA(jj).tstat);
    TractProfile(jj) = AFQ_TractProfileSet(TractProfile(jj),'vals','TstatMD',TstatsMD(jj).tstat);
    TractProfile(jj) = AFQ_TractProfileSet(TractProfile(jj),'vals','TstatRD',TstatsRD(jj).tstat);
end
{% endhighlight %}

Save the TractProfile data so you don't have to generate it again in the future. You can load it like you loaded the afq.mat file:

{% highlight matlab %}
save([var,'/Desktop/TractProfile_' datestr(now,'yyyy_mm_dd_HHMM')],'TractProfile');
{% endhighlight  %}

### Statistical Results

<img class="img-responsive" alt="" src="images/pvalue.png">

{% highlight matlab %}
mymap = [1 0 0
1 1 1];
crange = [0 1]; numfibers=200; radius = 5; subdivs = 100; cmap = mymap;

AFQ_RenderFibers(fg(5),'color',[.75 .75 .75],'tractprofile',TractProfile(5),'val','pADval','numfibers',numfibers,'cmap',cmap,'crange',crange,'radius',[1 5],'subplot',[4 2 1]);
t1 = readFileNifti(dt.files.t1);
AFQ_AddImageTo3dPlot(t1,[1,0,0],[],[0]);
title('Left Cingulum Cingulate','fontsize',18)
colorbar('delete');
set(gca,'ZTickLabel',[],'YTickLabel',[]);
set(gca, 'CLim', [0, 0.05]);
zlabel('AD');
ylabel([]);
zl1=zlim;
yl1=ylim;

AFQ_RenderFibers(fg(6),'color',[.75 .75 .75],'tractprofile',TractProfile(6),'val','pADval','numfibers',numfibers,'cmap',cmap,'crange',crange,'radius',[1 5],'subplot',[4 2 2],'camera',[90 0]);
t1 = readFileNifti(dt.files.t1);
AFQ_AddImageTo3dPlot(t1,[1,0,0],[],[0]);
title('Right Cingulum Cingulate','fontsize',18)
colorbar('delete');
set(gca,'ZTickLabel',[],'YTickLabel',[]);
set(gca, 'CLim', [0, 0.05]);
zlabel('AD');
ylabel([]);
zlim(zl1);

AFQ_RenderFibers(fg(5),'color',[.75 .75 .75],'tractprofile',TractProfile(5),'val','pFAval','numfibers',numfibers,'cmap',cmap,'crange',crange,'radius',[1 5],'subplot',[4 2 3]);
t1 = readFileNifti(dt.files.t1);
AFQ_AddImageTo3dPlot(t1,[1,0,0],[],[0]);
colorbar('delete');
set(gca,'ZTickLabel',[],'YTickLabel',[]);
set(gca, 'CLim', [0, 0.05]);
zlabel('FA');
ylabel([]);
zlim(zl1);

AFQ_RenderFibers(fg(6),'color',[.75 .75 .75],'tractprofile',TractProfile(6),'val','pFAval','numfibers',numfibers,'cmap',cmap,'crange',crange,'radius',[1 5],'subplot',[4 2 4],'camera',[90 0]);
t1 = readFileNifti(dt.files.t1);
AFQ_AddImageTo3dPlot(t1,[1,0,0],[],[0]);
colorbar('delete');
set(gca,'ZTickLabel',[],'YTickLabel',[]);
set(gca, 'CLim', [0, 0.05]);
zlabel('FA');
ylabel([]);
zlim(zl1);

AFQ_RenderFibers(fg(5),'color',[.75 .75 .75],'tractprofile',TractProfile(5),'val','pMDval','numfibers',numfibers,'cmap',cmap,'crange',crange,'radius',[1 5],'subplot',[4 2 5]);
t1 = readFileNifti(dt.files.t1);
AFQ_AddImageTo3dPlot(t1,[1,0,0],[],[0]);
colorbar('delete');
set(gca,'ZTickLabel',[],'YTickLabel',[]);
set(gca, 'CLim', [0, 0.05]);
zlabel('MD');
ylabel([]);

AFQ_RenderFibers(fg(6),'color',[.75 .75 .75],'tractprofile',TractProfile(6),'val','pMDval','numfibers',numfibers,'cmap',cmap,'crange',crange,'radius',[1 5],'subplot',[4 2 6],'camera',[90 0]);
t1 = readFileNifti(dt.files.t1);
AFQ_AddImageTo3dPlot(t1,[1,0,0],[],[0]);
colorbar('delete');
set(gca,'ZTickLabel',[],'YTickLabel',[]);
set(gca, 'CLim', [0, 0.05]);
zlabel('MD');
ylabel([]);
zlim(zl1);

AFQ_RenderFibers(fg(5),'color',[.75 .75 .75],'tractprofile',TractProfile(5),'val','pRDval','numfibers',numfibers,'cmap',cmap,'crange',crange,'radius',[1 5],'subplot',[4 2 7]);
t1 = readFileNifti(dt.files.t1);
AFQ_AddImageTo3dPlot(t1,[1,0,0],[],[0]);
colorbar('delete');
set(gca,'ZTickLabel',[],'YTickLabel',[]);
set(gca, 'CLim', [0, 0.05]);
zlabel('RD');
ylabel([]);
zlim(zl1);

AFQ_RenderFibers(fg(6),'color',[.75 .75 .75],'tractprofile',TractProfile(6),'val','pRDval','numfibers',numfibers,'cmap',cmap,'crange',crange,'radius',[1 5],'subplot',[4 2 8],'camera',[90 0]);
t1 = readFileNifti(dt.files.t1);
AFQ_AddImageTo3dPlot(t1,[1,0,0],[],[0]);
colorbar('delete');
set(gca,'ZTickLabel',[],'YTickLabel',[]);
set(gca, 'CLim', [0, 0.05]);
zlabel('RD');
ylabel([]);
zlim(zl1);
{% endhighlight %}
