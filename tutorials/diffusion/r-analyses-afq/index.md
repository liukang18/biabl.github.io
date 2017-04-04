---
layout: tutorials
title: R Statistical Analyses with AFQ Results
author: naomi
comments: true
date: 2017-04-04
---

## Objectives

After you complete this section, you should be able to:

1. Download R libraries and configure ~/.Rprofile
2. Run descriptive statistics
3. Combine AFQ results
4. Run statistical analyses on tract profiles
5. Graph group tract profiles and show statistical group differences

## Sync Data

Download your output tables locally:

```bash
rsync -rauv intj5@ssh.fsl.byu.edu:/fslhome/intj5/compute/analyses/EDSD/AFQ ~/Desktop/
rsync -rauv intj5@ssh.fsl.byu.edu:/fslhome/intj5/compute/analyses/EDSD/AFQ-CC ~/Desktop/
```

Make sure to also download the demographics file:

[https://drive.google.com/open?id=0B7gwoaKa2xaTVnFVS25KSzZRQWc](https://drive.google.com/open?id=0B7gwoaKa2xaTVnFVS25KSzZRQWc)

## Libraries

Download and install the appropriate R libraries:

```r
install.packages(c("reshape", "Rmisc", "ggplot2", "stringr", "gridExtra",
    "psych", "Hmisc", "colorRamps", "lattice", "formatR", "plotrix", "outliers",
    "sm", "rowr", "pastecs"))
```

You only need to do this ONCE. After you have downloaded the packages, you can simply call the packages in R when you need to use them. I prefer creating an ~/.Rprofile that loads automatically when R starts. In your terminal window:

```bash
vi ~/.Rprofile
```

Copy and paste the following into your new text file:

```bash
library(reshape)
library(Rmisc)
library(ggplot2)
library(stringr)
library(gridExtra)
library(psych)
library(Hmisc)
library(colorRamps)
library(lattice)
library(formatR)
library(plotrix)
library(outliers)
library(sm)
library(rowr)
library(pastecs)
```

Now when R loads, it will run the code automatically and load the libraries.

## Descriptive Statistics

Run the following in R and select your demographics file (*EDSD.csv*):

```r
mydata = read.table(file.choose(), sep = ",", header = TRUE)
describeBy(mydata, mydata$diagnosis)
```

You should get the following tables:

<hr>

|           | vars|  n| mean|       sd| median| trimmed|    mad| min| max| range|       skew|   kurtosis|        se|
|:----------|----:|--:|----:|--------:|------:|-------:|------:|---:|---:|-----:|----------:|----------:|---------:|
|subjID*    |    1| 10|  5.5| 3.027650|    5.5|   5.500| 3.7065|   1|  10|     9|  0.0000000| -1.5616364| 0.9574271|
|diagnosis* |    2| 10|  1.0| 0.000000|    1.0|   1.000| 0.0000|   1|   1|     0|        NaN|        NaN| 0.0000000|
|age        |    3| 10| 75.1| 6.690790|   75.5|  75.375| 5.9304|  63|  85|    22| -0.3284808| -1.1291737| 2.1158135|
|education  |    4| 10| 12.4| 3.657564|   11.0|  12.250| 2.9652|   8|  18|    10|  0.4350698| -1.6245619| 1.1566234|
|gender*    |    5| 10|  1.2| 0.421637|    1.0|   1.125| 0.0000|   1|   2|     1|  1.2807225| -0.3675000| 0.1333333|
|MMSE       |    6| 10| 22.8| 2.820559|   23.5|  23.125| 2.2239|  17|  26|     9| -0.8599269| -0.6356272| 0.8919392|

<hr>

|           | vars|  n|    mean|        sd| median|   trimmed|    mad| min| max| range|       skew|   kurtosis|        se|
|:----------|----:|--:|-------:|---------:|------:|---------:|------:|---:|---:|-----:|----------:|----------:|---------:|
|subjID*    |    1| 16| 18.5000| 4.7609523|   18.5| 18.500000| 5.9304|  11|  26|    15|  0.0000000| -1.4262408| 1.1902381|
|diagnosis* |    2| 16|  2.0000| 0.0000000|    2.0|  2.000000| 0.0000|   2|   2|     0|        NaN|        NaN| 0.0000000|
|age        |    3| 16| 64.3750| 6.1087369|   65.0| 64.642857| 7.4130|  53|  72|    19| -0.3472581| -1.3062690| 1.5271842|
|education  |    4| 16| 14.5625| 3.3856314|   13.0| 14.571429| 3.7065|  10|  19|     9|  0.1803887| -1.8331549| 0.8464079|
|gender*    |    5| 16|  1.4375| 0.5123475|    1.0|  1.428571| 0.0000|   1|   2|     1|  0.2287266| -2.0652902| 0.1280869|
|MMSE       |    6| 16| 29.0000| 1.3662601|   30.0| 29.142857| 0.0000|  26|  30|     4| -0.8822311| -0.7758291| 0.3415650|

<hr>

If you want to calculate the statistical differences between the groups and their Mini-Mental State Examination:

```r
anova(lm(MMSE ~ diagnosis, data = mydata))
```

Your results should give you a significant group difference as you would hope to see given that the MMSE is used to screen for dementia.

<hr>

|          | Df|   Sum Sq|  Mean Sq|  F value| Pr(>F)|
|:---------|--:|--------:|--------:|--------:|------:|
|diagnosis |  1| 236.5538| 236.5538| 57.00093|  1e-07|
|Residuals | 24|  99.6000|   4.1500|       NA|     NA|

<hr>

You probably want to check that there are no group differences in education:

```r
anova(lm(education ~ diagnosis, data = mydata))
```

Looks like the groups are not statistically difference in amount of education:

<hr>

|          | Df|    Sum Sq|  Mean Sq|  F value|    Pr(>F)|
|:---------|--:|---------:|--------:|--------:|---------:|
|diagnosis |  1|  28.77788| 28.77788| 2.362575| 0.1373573|
|Residuals | 24| 292.33750| 12.18073|       NA|        NA|

<hr>

## Combine Tables

Currently the AFQ output results are separated by diagnosis group and diffusion metrics. In order to graph and analyze group differences along the fiber tracts, we need to combine the AD and HC *.csv* files for each fiber group. While we are at it, we might as well also combine the files based on diffusion metrics (i.e., FA, AD, RD, and MD). Here's a list of output files you should have:

<div class="twocolumn">
<ul style="list-style-type:square">
<li>ad_callosum_forceps_major_ad.csv</li>
<li>ad_callosum_forceps_major_fa.csv</li>
<li>ad_callosum_forceps_major_md.csv</li>
<li>ad_callosum_forceps_major_rd.csv</li>
<li>ad_callosum_forceps_minor_ad.csv</li>
<li>ad_callosum_forceps_minor_fa.csv</li>
<li>ad_callosum_forceps_minor_md.csv</li>
<li>ad_callosum_forceps_minor_rd.csv</li>
<li>ad_left_arcuate_ad.csv</li>
<li>ad_left_arcuate_fa.csv</li>
<li>ad_left_arcuate_md.csv</li>
<li>ad_left_arcuate_rd.csv</li>
<li>ad_left_cingulum_cingulate_ad.csv</li>
<li>ad_left_cingulum_cingulate_fa.csv</li>
<li>ad_left_cingulum_cingulate_md.csv</li>
<li>ad_left_cingulum_cingulate_rd.csv</li>
<li>ad_left_cingulum_hippocampus_ad.csv</li>
<li>ad_left_cingulum_hippocampus_fa.csv</li>
<li>ad_left_cingulum_hippocampus_md.csv</li>
<li>ad_left_cingulum_hippocampus_rd.csv</li>
<li>ad_left_corticospinal_ad.csv</li>
<li>ad_left_corticospinal_fa.csv</li>
<li>ad_left_corticospinal_md.csv</li>
<li>ad_left_corticospinal_rd.csv</li>
<li>ad_left_ifof_ad.csv</li>
<li>ad_left_ifof_fa.csv</li>
<li>ad_left_ifof_md.csv</li>
<li>ad_left_ifof_rd.csv</li>
<li>ad_left_ilf_ad.csv</li>
<li>ad_left_ilf_fa.csv</li>
<li>ad_left_ilf_md.csv</li>
<li>ad_left_ilf_rd.csv</li>
<li>ad_left_slf_ad.csv</li>
<li>ad_left_slf_fa.csv</li>
<li>ad_left_slf_md.csv</li>
<li>ad_left_slf_rd.csv</li>
<li>ad_left_thalamic_radiation_ad.csv</li>
<li>ad_left_thalamic_radiation_fa.csv</li>
<li>ad_left_thalamic_radiation_md.csv</li>
<li>ad_left_thalamic_radiation_rd.csv</li>
<li>ad_left_uncinate_ad.csv</li>
<li>ad_left_uncinate_fa.csv</li>
<li>ad_left_uncinate_md.csv</li>
<li>ad_left_uncinate_rd.csv</li>
<li>ad_right_arcuate_ad.csv</li>
<li>ad_right_arcuate_fa.csv</li>
<li>ad_right_arcuate_md.csv</li>
<li>ad_right_arcuate_rd.csv</li>
<li>ad_right_cingulum_cingulate_ad.csv</li>
<li>ad_right_cingulum_cingulate_fa.csv</li>
<li>ad_right_cingulum_cingulate_md.csv</li>
<li>ad_right_cingulum_cingulate_rd.csv</li>
<li>ad_right_cingulum_hippocampus_ad.csv</li>
<li>ad_right_cingulum_hippocampus_fa.csv</li>
<li>ad_right_cingulum_hippocampus_md.csv</li>
<li>ad_right_cingulum_hippocampus_rd.csv</li>
<li>ad_right_corticospinal_ad.csv</li>
<li>ad_right_corticospinal_fa.csv</li>
<li>ad_right_corticospinal_md.csv</li>
<li>ad_right_corticospinal_rd.csv</li>
<li>ad_right_ifof_ad.csv</li>
<li>ad_right_ifof_fa.csv</li>
<li>ad_right_ifof_md.csv</li>
<li>ad_right_ifof_rd.csv</li>
<li>ad_right_ilf_ad.csv</li>
<li>ad_right_ilf_fa.csv</li>
<li>ad_right_ilf_md.csv</li>
<li>ad_right_ilf_rd.csv</li>
<li>ad_right_slf_ad.csv</li>
<li>ad_right_slf_fa.csv</li>
<li>ad_right_slf_md.csv</li>
<li>ad_right_slf_rd.csv</li>
<li>ad_right_thalamic_radiation_ad.csv</li>
<li>ad_right_thalamic_radiation_fa.csv</li>
<li>ad_right_thalamic_radiation_md.csv</li>
<li>ad_right_thalamic_radiation_rd.csv</li>
<li>ad_right_uncinate_ad.csv</li>
<li>ad_right_uncinate_fa.csv</li>
<li>ad_right_uncinate_md.csv</li>
<li>ad_right_uncinate_rd.csv</li>
<li>hc_callosum_forceps_major_ad.csv</li>
<li>hc_callosum_forceps_major_fa.csv</li>
<li>hc_callosum_forceps_major_md.csv</li>
<li>hc_callosum_forceps_major_rd.csv</li>
<li>hc_callosum_forceps_minor_ad.csv</li>
<li>hc_callosum_forceps_minor_fa.csv</li>
<li>hc_callosum_forceps_minor_md.csv</li>
<li>hc_callosum_forceps_minor_rd.csv</li>
<li>hc_left_arcuate_ad.csv</li>
<li>hc_left_arcuate_fa.csv</li>
<li>hc_left_arcuate_md.csv</li>
<li>hc_left_arcuate_rd.csv</li>
<li>hc_left_cingulum_cingulate_ad.csv</li>
<li>hc_left_cingulum_cingulate_fa.csv</li>
<li>hc_left_cingulum_cingulate_md.csv</li>
<li>hc_left_cingulum_cingulate_rd.csv</li>
<li>hc_left_cingulum_hippocampus_ad.csv</li>
<li>hc_left_cingulum_hippocampus_fa.csv</li>
<li>hc_left_cingulum_hippocampus_md.csv</li>
<li>hc_left_cingulum_hippocampus_rd.csv</li>
<li>hc_left_corticospinal_ad.csv</li>
<li>hc_left_corticospinal_fa.csv</li>
<li>hc_left_corticospinal_md.csv</li>
<li>hc_left_corticospinal_rd.csv</li>
<li>hc_left_ifof_ad.csv</li>
<li>hc_left_ifof_fa.csv</li>
<li>hc_left_ifof_md.csv</li>
<li>hc_left_ifof_rd.csv</li>
<li>hc_left_ilf_ad.csv</li>
<li>hc_left_ilf_fa.csv</li>
<li>hc_left_ilf_md.csv</li>
<li>hc_left_ilf_rd.csv</li>
<li>hc_left_slf_ad.csv</li>
<li>hc_left_slf_fa.csv</li>
<li>hc_left_slf_md.csv</li>
<li>hc_left_slf_rd.csv</li>
<li>hc_left_thalamic_radiation_ad.csv</li>
<li>hc_left_thalamic_radiation_fa.csv</li>
<li>hc_left_thalamic_radiation_md.csv</li>
<li>hc_left_thalamic_radiation_rd.csv</li>
<li>hc_left_uncinate_ad.csv</li>
<li>hc_left_uncinate_fa.csv</li>
<li>hc_left_uncinate_md.csv</li>
<li>hc_left_uncinate_rd.csv</li>
<li>hc_right_arcuate_ad.csv</li>
<li>hc_right_arcuate_fa.csv</li>
<li>hc_right_arcuate_md.csv</li>
<li>hc_right_arcuate_rd.csv</li>
<li>hc_right_cingulum_cingulate_ad.csv</li>
<li>hc_right_cingulum_cingulate_fa.csv</li>
<li>hc_right_cingulum_cingulate_md.csv</li>
<li>hc_right_cingulum_cingulate_rd.csv</li>
<li>hc_right_cingulum_hippocampus_ad.csv</li>
<li>hc_right_cingulum_hippocampus_fa.csv</li>
<li>hc_right_cingulum_hippocampus_md.csv</li>
<li>hc_right_cingulum_hippocampus_rd.csv</li>
<li>hc_right_corticospinal_ad.csv</li>
<li>hc_right_corticospinal_fa.csv</li>
<li>hc_right_corticospinal_md.csv</li>
<li>hc_right_corticospinal_rd.csv</li>
<li>hc_right_ifof_ad.csv</li>
<li>hc_right_ifof_fa.csv</li>
<li>hc_right_ifof_md.csv</li>
<li>hc_right_ifof_rd.csv</li>
<li>hc_right_ilf_ad.csv</li>
<li>hc_right_ilf_fa.csv</li>
<li>hc_right_ilf_md.csv</li>
<li>hc_right_ilf_rd.csv</li>
<li>hc_right_slf_ad.csv</li>
<li>hc_right_slf_fa.csv</li>
<li>hc_right_slf_md.csv</li>
<li>hc_right_slf_rd.csv</li>
<li>hc_right_thalamic_radiation_ad.csv</li>
<li>hc_right_thalamic_radiation_fa.csv</li>
<li>hc_right_thalamic_radiation_md.csv</li>
<li>hc_right_thalamic_radiation_rd.csv</li>
<li>hc_right_uncinate_ad.csv</li>
<li>hc_right_uncinate_fa.csv</li>
<li>hc_right_uncinate_md.csv</li>
<li>hc_right_uncinate_rd.csv</li>
</ul>
</div>

<hr>

Let's start by creating a new R script:

```bash
vi ~/Desktop/AFQ/combine-tables.r
```

Copy and paste the following code:

```r
projDir = tk_choose.dir(caption = "Select the AFQ stats directory")
demogr = read.table(tk_choose.files(caption = "Select demographic file"),
    sep = ",", header = TRUE)
rawdata = c("callosum_forceps_major", "callosum_forceps_minor", "left_arcuate",
    "left_cingulum_cingulate", "left_cingulum_hippocampus", "left_corticospinal",
    "left_ifof", "left_ilf", "left_slf", "left_thalamic_radiation", "left_uncinate",
    "right_arcuate", "right_cingulum_cingulate", "right_cingulum_hippocampus",
    "right_corticospinal", "right_ifof", "right_ilf", "right_slf", "right_thalamic_radiation",
    "right_uncinate")
for (n in 1:length(rawdata)) {
    files = list.files(paste(projDir, "/", sep = ""), pattern = rawdata[n],
        full.names = TRUE)
    mydata = data.frame()
    for (i in 1:length(files)) {
        temp = read.table(files[i], sep = ",", header = FALSE)
        temp$variable = files[i]
        if (nrow(temp) == 10) {
            temp = cbind(demogr[1:10, ], temp)
        } else {
            temp = cbind(demogr[11:26, ], temp)
        }
        mydata = rbind(mydata, temp)
    }
    tempvar = strsplit(as.character(mydata$variable), "/")
    mydata$variable = sapply(tempvar, "[[", length(tempvar[[1]]))
    tempvar = strsplit(as.character(mydata$variable), "_")
    mydata$dtiscalar = sapply(tempvar, "[[", length(tempvar[[1]]))
    mydata$dtiscalar = substr(mydata$dtiscalar, 1, 2)
    tempid = strsplit(as.character(mydata$V1), "/")
    mydata = mydata[c(1:6, 108, 7:106)]
    write.table(mydata, paste(projDir, "/", rawdata[n], ".csv", sep = ""),
        row.names = F, sep = ",")
}
```

In the R.app, run the new script:

```r
source("~/Desktop/AFQ/combine-tables.r")
```

Do the same for the corpus callosum segmentations:

```bash
vi ~/Desktop/AFQ-CC/combine-tables.r
```

Copy and paste the following code:

```r
projDir = tk_choose.dir(caption = "Select the AFQ-CC stats directory")
demogr = read.table(tk_choose.files(caption = "Select demographic file"),
    sep = ",", header = TRUE)
rawdata = c("cc_occipital", "cc_post_parietal", "cc_sup_parietal", "cc_motor",
    "cc_sup_frontal", "cc_ant_frontal", "cc_orb_frontal", "cc_temporal")
for (n in 1:length(rawdata)) {
    files = list.files(paste(projDir, "/", sep = ""), pattern = rawdata[n],
        full.names = TRUE)
    mydata = data.frame()
    for (i in 1:length(files)) {
        temp = read.table(files[i], sep = ",", header = FALSE)
        temp$variable = files[i]
        if (nrow(temp) == 10) {
            temp = cbind(demogr[1:10, ], temp)
        } else {
            temp = cbind(demogr[11:26, ], temp)
        }
        mydata = rbind(mydata, temp)
    }
    tempvar = strsplit(as.character(mydata$variable), "/")
    mydata$variable = sapply(tempvar, "[[", length(tempvar[[1]]))
    tempvar = strsplit(as.character(mydata$variable), "_")
    mydata$dtiscalar = sapply(tempvar, "[[", length(tempvar[[1]]))
    mydata$dtiscalar = substr(mydata$dtiscalar, 1, 2)
    tempid = strsplit(as.character(mydata$V1), "/")
    mydata = mydata[c(1:6, 108, 7:106)]
    write.table(mydata, paste(projDir, "/", rawdata[n], ".csv", sep = ""),
        row.names = F, sep = ",")
}
```

In the R.app, run the new script:

```r
source("~/Desktop/AFQ-CC/combine-tables.r")
```

## Statistics

Recall that the tract profiles consist of 100 segments along the tract for each of the diffusion metrics. You need to statistically evaluate each 100 segment to determine if there are any group differences. That comes out to 100 segments x 4 diffusion properties x 20 fiber tracts = 8,000 t-tests comparing AD versus HC. Not only do we want to run these tests automatically, but at the very least we want to output the p-values to a table. Create the directory to output the p-value tables:

```bash
mkdir -p ~/Desktop/AFQ/stats/pvalue/fa
mkdir -p ~/Desktop/AFQ/stats/pvalue/rd
mkdir -p ~/Desktop/AFQ/stats/pvalue/ad
mkdir -p ~/Desktop/AFQ/stats/pvalue/md
mkdir -p ~/Desktop/AFQ-CC/stats/pvalue/fa
mkdir -p ~/Desktop/AFQ-CC/stats/pvalue/rd
mkdir -p ~/Desktop/AFQ-CC/stats/pvalue/ad
mkdir -p ~/Desktop/AFQ-CC/stats/pvalue/md
```

Create an R script:

```bash
vi ~/Desktop/AFQ/pvalue.R
```

Copy and paste:

```r
metric <- readline(prompt = "What DTI metric do you want to analyze (fa, rd, md, ad): ")
projDir = tk_choose.dir(caption = "Select the AFQ stats directory")
rawdata = c("callosum_forceps_major", "callosum_forceps_minor", "left_arcuate",
    "left_cingulum_cingulate", "left_cingulum_hippocampus", "left_corticospinal",
    "left_ifof", "left_ilf", "left_slf", "left_thalamic_radiation", "left_uncinate",
    "right_arcuate", "right_cingulum_cingulate", "right_cingulum_hippocampus",
    "right_corticospinal", "right_ifof", "right_ilf", "right_slf", "right_thalamic_radiation",
    "right_uncinate")
for (n in 1:length(rawdata)) {
    mydata = read.table(paste(projDir, "/", rawdata[n], ".csv", sep = ""),
        sep = ",", header = TRUE)
    mydata = subset(mydata, dtiscalar == metric)
    pvalue = data.frame()
    for (x in seq(1:100)) {
        node = paste("V", x, sep = "")
        diagnosis = anova(lm(mydata[[node]] ~ diagnosis, data = mydata))[5][1,]
        diagnosis = data.frame(diagnosis)
        pvalue = rbind(pvalue, diagnosis)
    }
    write.csv(pvalue, paste(projDir, "/pvalue/", metric, "/", rawdata[n],
        ".csv", sep = ""), row.names = T)
}
```

In the R.app, run the new script:

```r
source("~/Desktop/AFQ/pvalue.r")
```

Do the same for the corpus callosum segmentations:

```bash
vi ~/Desktop/AFQ-CC/pvalue.R
```

Copy and paste:

```r
metric <- readline(prompt = "What DTI metric do you want to analyze (fa, rd, md, ad): ")
projDir = tk_choose.dir(caption = "Select the AFQ-CC stats directory")
rawdata = c("cc_occipital", "cc_post_parietal", "cc_sup_parietal", "cc_motor",
    "cc_sup_frontal", "cc_ant_frontal", "cc_orb_frontal", "cc_temporal")
for (n in 1:length(rawdata)) {
    mydata = read.table(paste(projDir, "/", rawdata[n], ".csv", sep = ""),
        sep = ",", header = TRUE)
    mydata = subset(mydata, dtiscalar == metric)
    pvalue = data.frame()
    for (x in seq(1:100)) {
        node = paste("V", x, sep = "")
        diagnosis = anova(lm(mydata[[node]] ~ diagnosis, data = mydata))[5][1,]
        diagnosis = data.frame(diagnosis)
        pvalue = rbind(pvalue, diagnosis)
    }
    write.csv(pvalue, paste(projDir, "/pvalue/", metric, "/", rawdata[n],
        ".csv", sep = ""), row.names = T)
}
```

In the R.app, run the new script:

```r
source("~/Desktop/AFQ-CC/pvalue.r")
```

## Graphs

Now we can combine the data and p-value results into a graph. Now we've already created similar graphs using MATLAB [http://biabl.github.io/tutorials/diffusion/afq-plot-render/](http://biabl.github.io/tutorials/diffusion/afq-plot-render/), but now we can control the look of the graphs in R and add shaded information to represent statistical group differences. Create the directories to output the graphs:

```bash
mkdir -p ~/Desktop/AFQ/stats/graphs/fa
mkdir -p ~/Desktop/AFQ/stats/graphs/rd
mkdir -p ~/Desktop/AFQ/stats/graphs/ad
mkdir -p ~/Desktop/AFQ/stats/graphs/md
mkdir -p ~/Desktop/AFQ-CC/stats/graphs/fa
mkdir -p ~/Desktop/AFQ-CC/stats/graphs/rd
mkdir -p ~/Desktop/AFQ-CC/stats/graphs/ad
mkdir -p ~/Desktop/AFQ-CC/stats/graphs/md
```

Create an R script:

```bash
vi ~/Desktop/AFQ/graphs.R
```

Copy and paste:

```r
metric <- readline(prompt = "What DTI metric do you want to analyze (fa, rd, md, ad): ")
projDir = tk_choose.dir(caption = "Select the AFQ stats directory")
rawdata = c("callosum_forceps_major", "callosum_forceps_minor", "left_arcuate",
    "left_cingulum_cingulate", "left_cingulum_hippocampus", "left_corticospinal",
    "left_ifof", "left_ilf", "left_slf", "left_thalamic_radiation", "left_uncinate",
    "right_arcuate", "right_cingulum_cingulate", "right_cingulum_hippocampus",
    "right_corticospinal", "right_ifof", "right_ilf", "right_slf", "right_thalamic_radiation",
    "right_uncinate")
for (n in 1:length(rawdata)) {
    mydata = read.table(paste(projDir, "/", rawdata[n], ".csv", sep = ""),
        sep = ",", header = TRUE)
    pvalue = read.table(paste(projDir, "/pvalue/", metric, "/", rawdata[n],
        ".csv", sep = ""), sep = ",", header = T, row.names = 1)
    melted = subset(melt(mydata, id = c("subjID", "diagnosis", "age", "education",
        "gender", "MMSE", "dtiscalar")), dtiscalar == metric)
    melted = group.STDERR(value ~ variable * diagnosis, melted)
    p = ggplot() + geom_line(data = melted, aes(x = as.numeric(variable),
        y = value.mean, colour = diagnosis, linetype = diagnosis), size = 0.5) +
        geom_ribbon(show.legend = F, data = melted, aes(x = as.numeric(variable),
            ymin = value.lower, ymax = value.upper, fill = diagnosis),
            alpha = 0.25) + scale_color_manual(values = c("red", "black")) +
        scale_fill_manual(values = c("red", "black")) + scale_linetype_manual(values = c("solid",
        "solid")) + theme_bw() + theme(legend.title = element_blank(),
        legend.position = "right", plot.title = element_text(size = 10),
        text = element_text(size = 10)) + ggtitle(toupper(str_replace_all(rawdata[n],
        "_", " "))) + xlab("% Distance Along Fiber Tract") + ylab(paste("Mean",
        toupper(metric), sep = " "))
    pvalue = round(pvalue, digits = 3)
    pvalue = subset(pvalue, pvalue$diagnosis <= 0.05)
    if (empty(pvalue) == FALSE) {
        for (z in 1:length(row.names(pvalue))) {
            min = as.numeric(row.names(pvalue))[z]
            max = min + 1
            lower = min(melted$lower)
            upper = max(melted$upper)
            p = p + annotate("rect", xmin = min, xmax = max, ymin = lower,
                ymax = upper, alpha = 0.25)
        }
    }
    pdf(paste(projDir, "/graphs/", metric, "/", rawdata[n], ".pdf", sep = ""),
        width = 5, height = 3)
    print(p)
    dev.off()
}
```

In the R.app, run the new script:

```r
source("~/Desktop/AFQ/graphs.r")
```

Do the same for the corpus callosum segmentations:

```bash
vi ~/Desktop/AFQ-CC/graphs.R
```

Copy and paste:

```r
metric <- readline(prompt = "What DTI metric do you want to analyze (fa, rd, md, ad): ")
projDir = tk_choose.dir(caption = "Select the AFQ-CC stats directory")
rawdata = c("cc_occipital", "cc_post_parietal", "cc_sup_parietal", "cc_motor",
    "cc_sup_frontal", "cc_ant_frontal", "cc_orb_frontal", "cc_temporal")
for (n in 1:length(rawdata)) {
    mydata = read.table(paste(projDir, "/", rawdata[n], ".csv", sep = ""),
        sep = ",", header = TRUE)
    pvalue = read.table(paste(projDir, "/pvalue/", metric, "/", rawdata[n],
        ".csv", sep = ""), sep = ",", header = T, row.names = 1)
    melted = subset(melt(mydata, id = c("subjID", "diagnosis", "age", "education",
        "gender", "MMSE", "dtiscalar")), dtiscalar == metric)
    melted = group.STDERR(value ~ variable * diagnosis, melted)
    p = ggplot() + geom_line(data = melted, aes(x = as.numeric(variable),
        y = value.mean, colour = diagnosis, linetype = diagnosis), size = 0.5) +
        geom_ribbon(show.legend = F, data = melted, aes(x = as.numeric(variable),
            ymin = value.lower, ymax = value.upper, fill = diagnosis),
            alpha = 0.25) + scale_color_manual(values = c("red", "black")) +
        scale_fill_manual(values = c("red", "black")) + scale_linetype_manual(values = c("solid",
        "solid")) + theme_bw() + theme(legend.title = element_blank(),
        legend.position = "right", plot.title = element_text(size = 10),
        text = element_text(size = 10)) + ggtitle(toupper(str_replace_all(rawdata[n],
        "_", " "))) + xlab("% Distance Along Fiber Tract") + ylab(paste("Mean",
        toupper(metric), sep = " "))
    pvalue = round(pvalue, digits = 3)
    pvalue = subset(pvalue, pvalue$diagnosis <= 0.05)
    if (empty(pvalue) == FALSE) {
        for (z in 1:length(row.names(pvalue))) {
            min = as.numeric(row.names(pvalue))[z]
            max = min + 1
            lower = min(melted$lower)
            upper = max(melted$upper)
            p = p + annotate("rect", xmin = min, xmax = max, ymin = lower,
                ymax = upper, alpha = 0.25)
        }
    }
    pdf(paste(projDir, "/graphs/", metric, "/", rawdata[n], ".pdf", sep = ""),
        width = 5, height = 3)
    print(p)
    dev.off()
}
```

In the R.app, run the new script:

```r
source("~/Desktop/AFQ-CC/graphs.r")
```