---
layout: tutorials
title: R Statistical Analyses with TRACULA Results
author: naomi
comments: true
date: 2017-04-04
---

## Objectives

After you complete this section, you should be able to:

1. Combine TRACULA results
2. Run statistical analyses on tract profiles
3. Graph group tract profiles and show statistical group differences

## Sync Data

Download your output tables locally:

```bash
mkdir ~/Desktop/TRACULA
rsync -rauv intj5@ssh.fsl.byu.edu:/fslhome/intj5/compute/analyses/EDSD/TRACULA/data/stats/ ~/Desktop/TRACULA
```

Make sure to also download the demographics file:

[https://drive.google.com/open?id=0B7gwoaKa2xaTVnFVS25KSzZRQWc](https://drive.google.com/open?id=0B7gwoaKa2xaTVnFVS25KSzZRQWc)

## Combine Tables

Currently the AFQ output results are separated by diffusion metrics. In order to graph and analyze group differences along the fiber tracts, we need to combine the files based on diffusion metrics (i.e., FA, AD, RD, and MD).

Let's start by creating a new R script:

```bash
vi ~/Desktop/TRACULA/combine-tables.r
```

Copy and paste the following code:

```r
projDir = tk_choose.dir(caption = "Select the TRACULA stats directory")
demogr = read.table(tk_choose.files(caption = "Select demographic file"),
    sep = ",", header = TRUE)
rawdata = c("fmajor_PP.avg33_mni_bbr", "fminor_PP.avg33_mni_bbr", "lh.atr_PP.avg33_mni_bbr",
    "lh.cab_PP.avg33_mni_bbr", "lh.ccg_PP.avg33_mni_bbr", "lh.cst_AS.avg33_mni_bbr",
    "lh.ilf_AS.avg33_mni_bbr", "lh.slfp_PP.avg33_mni_bbr", "lh.slft_PP.avg33_mni_bbr",
    "lh.unc_AS.avg33_mni_bbr", "rh.atr_PP.avg33_mni_bbr", "rh.cab_PP.avg33_mni_bbr",
    "rh.ccg_PP.avg33_mni_bbr", "rh.cst_AS.avg33_mni_bbr", "rh.ilf_AS.avg33_mni_bbr",
    "rh.slfp_PP.avg33_mni_bbr", "rh.slft_PP.avg33_mni_bbr", "rh.unc_AS.avg33_mni_bbr")
for (n in 1:length(rawdata)) {
    files = list.files(paste(projDir, "/", sep = ""), pattern = rawdata[n],
        full.names = TRUE)
    matches = grep(pattern = "^.*avg33_mni_bbr.*_Avg.txt", files, value = T)
    mydata = data.frame()
    for (i in 1:length(matches)) {
        temp = read.table(matches[i], sep = " ", header = TRUE)
        temp = data.frame(t(temp[1:(length(temp)-1)]))
        temp$studyid = rownames(temp)
        rownames(temp) = NULL
        temp[temp == ""] <- NA
        temp$variable = matches[i]
        mydata = rbind(mydata, temp)
    }
    tempvar = strsplit(as.character(mydata$variable), "/")
    mydata$variable = sapply(tempvar, "[[", length(tempvar[[1]]))
    tempvar = strsplit(as.character(mydata$variable), "_")
    mydata$dtiscalar = sapply(tempvar, "[[", length(tempvar[[1]]) - 1)
    mydata$dtiscalar = substr(mydata$dtiscalar, 5, 6)
    mydata = mydata[c((length(mydata) - 2), (length(mydata)), 1:(length(mydata) -
        3))]
    mydata = merge(mydata, demogr, by = 1, all.x = TRUE)
    mydata = mydata[c(1, (length(mydata) - 4):length(mydata), 2:(length(mydata) -
        5))]
    mydata[mydata == "NaN"] <- NA
    write.table(mydata, paste(projDir, "/", rawdata[n], ".csv", sep = ""),
        row.names = F, sep = ",")
}
```

In the R.app, run the new script:

```r
source("~/Desktop/TRACULA/combine-tables.r")
```

## Statistics

Recall that the tract profiles consist of segments along the tract for each of the diffusion metrics. You need to statistically evaluate each segment to determine if there are any group differences. Not only do we want to run these tests automatically, but at the very least we want to output the p-values to a table. Create the directories to output the p-value tables:

```bash
mkdir -p ~/Desktop/TRACULA/pvalue/FA
mkdir -p ~/Desktop/TRACULA/pvalue/RD
mkdir -p ~/Desktop/TRACULA/pvalue/AD
mkdir -p ~/Desktop/TRACULA/pvalue/MD
```

Create an R script:

```bash
vi ~/Desktop/TRACULA/pvalue.r
```

Copy and paste:

```r
metric <- readline(prompt = "What DTI metric do you want to analyze (FA, RD, MD, AD): ")
projDir = tk_choose.dir(caption = "Select the TRACULA directory")
rawdata = c("fmajor_PP.avg33_mni_bbr", "fminor_PP.avg33_mni_bbr", "lh.atr_PP.avg33_mni_bbr",
    "lh.cab_PP.avg33_mni_bbr", "lh.ccg_PP.avg33_mni_bbr", "lh.cst_AS.avg33_mni_bbr",
    "lh.ilf_AS.avg33_mni_bbr", "lh.slfp_PP.avg33_mni_bbr", "lh.slft_PP.avg33_mni_bbr",
    "lh.unc_AS.avg33_mni_bbr", "rh.atr_PP.avg33_mni_bbr", "rh.cab_PP.avg33_mni_bbr",
    "rh.ccg_PP.avg33_mni_bbr", "rh.cst_AS.avg33_mni_bbr", "rh.ilf_AS.avg33_mni_bbr",
    "rh.slfp_PP.avg33_mni_bbr", "rh.slft_PP.avg33_mni_bbr", "rh.unc_AS.avg33_mni_bbr")
for (n in 1:length(rawdata)) {
    mydata = read.table(paste(projDir, "/", rawdata[n], ".csv", sep = ""),
        sep = ",", header = TRUE)
    mydata = subset(mydata, dtiscalar == metric)
    pvalue = data.frame()
    for (x in 1:(length(mydata)-7)) {
        node = paste("X", x, sep = "")
        if (sum(is.na(mydata[[node]])) < 20) {
        	diagnosis = anova(lm(mydata[[node]] ~ diagnosis, data = mydata, na.action=na.omit))[5][1,]
        	diagnosis = data.frame(diagnosis)
        	pvalue = rbind(pvalue, diagnosis)
        }
    }
    write.csv(pvalue, paste(projDir, "/pvalue/", metric, "/", rawdata[n],
        ".csv", sep = ""), row.names = T)
}
```

In the R.app, run the new script:

```r
source("~/Desktop/TRACULA/pvalue.r")
```

## Graphs

Now we can combine the data and p-value results into a graph. Create the directories to output the graphs:

```bash
mkdir -p ~/Desktop/TRACULA/graphs/fa
mkdir -p ~/Desktop/TRACULA/graphs/rd
mkdir -p ~/Desktop/TRACULA/graphs/ad
mkdir -p ~/Desktop/TRACULA/graphs/md
```

Create an R script:

```bash
vi ~/Desktop/TRACULA/graphs.r
```

Copy and paste:

```r
metric <- readline(prompt = "What DTI metric do you want to analyze (FA, RD, MD, AD): ")
projDir = tk_choose.dir(caption = "Select the TRACULA directory")
rawdata = c("fmajor_PP.avg33_mni_bbr", "fminor_PP.avg33_mni_bbr", "lh.atr_PP.avg33_mni_bbr",
    "lh.cab_PP.avg33_mni_bbr", "lh.ccg_PP.avg33_mni_bbr", "lh.cst_AS.avg33_mni_bbr",
    "lh.ilf_AS.avg33_mni_bbr", "lh.slfp_PP.avg33_mni_bbr", "lh.slft_PP.avg33_mni_bbr",
    "lh.unc_AS.avg33_mni_bbr", "rh.atr_PP.avg33_mni_bbr", "rh.cab_PP.avg33_mni_bbr",
    "rh.ccg_PP.avg33_mni_bbr", "rh.cst_AS.avg33_mni_bbr", "rh.ilf_AS.avg33_mni_bbr",
    "rh.slfp_PP.avg33_mni_bbr", "rh.slft_PP.avg33_mni_bbr", "rh.unc_AS.avg33_mni_bbr")
for (n in 1:length(rawdata)) {
    mydata = read.table(paste(projDir, "/", rawdata[n], ".csv", sep = ""),
        sep = ",", header = TRUE)
    pvalue = read.table(paste(projDir, "/pvalue/", metric, "/", rawdata[n],
        ".csv", sep = ""), sep = ",", header = T, row.names = 1)
    melted = subset(melt(mydata, id = c("studyid", "diagnosis", "age", "education",
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
            lower = min(melted$value.lower)
            upper = max(melted$value.upper)
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
source("~/Desktop/TRACULA/graphs.r")
```
