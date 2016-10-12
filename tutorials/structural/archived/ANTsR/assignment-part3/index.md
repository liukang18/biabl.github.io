---
title: "ANTsR Analyses"
author: "Assignment"
date: "Monday, November 23, 2015"
output:
  slidy_presentation:
    css: ~/git/byu-f15/stylesheets/mystyle.css
    highlight: haddock
    incremental: no
    keep_md: yes
---

## Log Into Supercomputer

Change your <username>

```bash
ssh <username>@ssh.fsl.byu.edu
```

## Launch R

Load Environmental Variables

```bash
module load r/3.2.1
```
Launch R

```R
R
```

Let's load our packages.

```R
library(ANTsR)
```

## Set path

If doing the cortical thickness analysis (cortical thickness images):

```R
setwd("~/compute/projects/corticalthickness/data/")
```

If doing the tensor based analysis (log jacobian images):

```R
setwd("~/compute/projects/tbm/data/")
```

If doing the voxel based analysis (modulated gray matter images):

```R
setwd("~/compute/projects/vbm/data/")
```

## Import Files

Import files and smooth - The following code creates a list of files in your working directory (*files*). Any empty list is created for when the images are smoothed they are added to the empty list (*ilist*). For each image in the *files* list, the are imported and smoothed and then added to the *ilist* list. 

```R
files<-list.files()
```

When you type `files` you should output a list of files:

```R
 [1] "1304_082907.nii.gz" "1306_091707.nii.gz" "1307_121807.nii.gz"
 [4] "1308_111207.nii.gz" "1310_010208.nii.gz" "1315_112808.nii.gz"
 [7] "1319_042009.nii.gz" "1320_082409.nii.gz" "1326_061810.nii.gz"
[10] "1327_031011.nii.gz" "2304_012808.nii.gz" "2307_031708.nii.gz"
[13] "2310_010709.nii.gz" "2314_032709.nii.gz" "2316_062409.nii.gz"
[16] "2317_123009.nii.gz" "2320_042310.nii.gz" "2323_083110.nii.gz"
[19] "2324_120810.nii.gz" "2370_020111.nii.gz"
```

## Smooth Images

```R
ilist<-list()
for ( i in 1:length(files) )
{
  img<-antsImageRead(files[i])
  img<-smoothImage(img,1.5)
  ilist[i]<-img
}
```

Convert to matrices - A simple mask is created to know what is and is not data to analyze. You don't want to analyze nonbrain data. The values are extracted from each voxel to form a 3D matrix.

```R
mask<-getMask(antsImageRead(files[1]))
mat<-imageListToMatrix(ilist, mask)
```

## Load Demographics Files

Demographic data can be anything from group ID, age, and gender. Data can also include neuropsych outcomes etc. To make life easy, your demographics file sound have the same study IDs as your MRI files and also be in alphanumeric order.

```R
mydata<-read.table("~/compute/projects/demographics.csv", header=TRUE,sep=",")
```

Subset data to demographics and desired variable column. For your first analyses let's just compared TBI versus OI, so create a variable *var1* that contains group diagnosis, which is column 2 out of the whole demographic file.

```R
vardata<-mydata[c(1:2)]
var1<-vardata[,2]
```

Get rid of any missing data. R does **NOT** like missing data and will often not run if there's missing data.

```R
vardata<-na.omit(vardata)
```

## Merge Data

Taking the subject ID information from the files. Namely your files should be labeled as subject ID (again to make life easier). Since the names in the *files* list contain not only the subject ID but also the file type (.nii.gz), we want just the subject ID which is 10 characters long. We can then merge the *study id* list with the demographics *vardata* and make sure we just have demographics data for the participants with images. 

```R
studyid=substr(files,1,11)
studyid=data.frame(studyid)
vardata=merge(vardata,studyid,all.y=TRUE)
varlist<-rownames(vardata)
varlist<-as.numeric(varlist)
varlist<-list(varlist)
for(i in varlist){
varmat<-subset(mat[i,])
}
```

## Run Analysis

By running a simple linear regression with a single independent variable (*var1*), we are able to compared TBI versus OI:

```R
voxels<-ncol(varmat)
regpval<-matrix(nrow=1,ncol=voxels)
regtstat<-matrix(nrow=1,ncol=voxels)
for (i in 1:voxels){
vox<-varmat[,i]
regfit<-lm(vox~var1)
regsum<-summary(regfit)
regpval[,i]<-regsum$coefficients[2,4]
regtstat[,i]<-regsum$coefficients[2,3]
}
```

## Convert to statistical images

The data is in the form of a 3D matrix and needs to be converted into a visual NIfTI image. Make sure the change the ***<username>*** to your supercomputer username:

```R
i.regpval<-makeImage(mask,regpval)
antsImageWrite(i.regpval,file="/fslhome/<username>/compute/projects/corticalthickness/regpval.nii.gz")
i.regtstat<-makeImage(mask,regtstat)
antsImageWrite(i.regtstat,file="/fslhome/<username>/compute/projects/corticalthickness/regtstat.nii.gz")
```

## Save Data on Desktop

Log out of R (you do not need to save workspace):

```R
q()
```

Log out of supercomputer:

```bash
exit
```

Sync data:

```bash
rsync -rauv --exclude="data" <username>@ssh.fsl.byu.edu:~/compute/projects/ ~/Desktop/
```

## View Results with Mango

You can download the NKI10andUnder template here:

[http://files.figshare.com/1363203/NKI10andUnder.zip](http://files.figshare.com/1363203/NKI10andUnder.zip)

Load the MPRAGE image with any of your regtstat.nii.gz as an overlay.
