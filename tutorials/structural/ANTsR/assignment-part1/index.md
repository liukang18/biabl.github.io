---
title: "Installing ANTsR on the Supercomputer"
author: "Assignment"
date: "Monday, November 16, 2015"
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

## Load Environmental Variables

```bash
module load cmake/2.8.10
module load r/3.2.1
```

## Set Package Directory

R relies on packages to run functions. There are currently 7462 packages available. Since any normal user probably only uses <10% of packages, you have to install the packages you will want to use. You need to create directory in your home directory to keep these packages. Once you've created this directory, you also need to set your R environment to know where to look for this directory:

```bash
mkdir ~/R
echo 'R_LIBS_USER="~/R"' >  $HOME/.Renviron
```

## Install First Package

The first package needs to be downloaded directly from source, all the other packages can be installed from within R:

```bash
cd ~/R
wget https://github.com/Rexamine/stringi/archive/master.zip
unzip master.zip
R CMD INSTALL stringi-master
```
## Launch R

```
R
```

Let's install a couple of packages and their dependencies:

```R
options(download.file.method = "wget")
pkgnames <- c("Rcpp", "tools", "methods", "drat")
install.packages(pkgnames, dependencies = TRUE, repos="http://cran.rstudio.com/")
```

```R
pkgnames <- c("stringr", "evaluate", "knitr", "magrittr")
install.packages(pkgnames, dependencies = FALSE, repos="http://cran.rstudio.com/")
```

## Install ANTsR

```R
drat::addRepo("ANTs-R")
install.packages("ANTsR")
```

## Done

Now anytime you want to run R, all you have to do is:

```bash
module load r/3.2.1
R
```

