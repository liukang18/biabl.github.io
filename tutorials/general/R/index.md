---
layout: tutorials
title: Learn R
author: naomi
comments: true
date: 2016-08-16
---

## Install Swirl on Supercomputer

R has already been installed on the supercomputer. Log into the supercomputer:

{% highlight bash %}
ssh USERNAME@ssh.fsl.byu.edu
{% endhighlight %}

Load the environmental variables so that R will run on the supercomputer:

{% highlight bash %}
module load cmake/2.8.10
module load r/3.1.1
{% endhighlight %}

R relies on packages to run functions. There are currently 7462 packages available. Since any normal user probably only uses <10% of packages, you have to install the packages you will want to use. You need to create directory in your home directory to keep these packages. Once you've created this directory, you also need to set your R environment to know where to look for this directory:

{% highlight bash %}
mkdir ~/apps/R
echo 'R_LIBS_USER="~/apps/R"' >  $HOME/.Renviron
{% endhighlight %}

The first package needs to be downloaded directly from source, all the other packages can be installed from within R:

{% highlight bash %}
cd ~/apps/R
wget https://github.com/Rexamine/stringi/archive/master.zip
unzip master.zip && rm master.zip
R CMD INSTALL stringi-master
{% endhighlight %}

Launch R:

{% highlight bash %}
R
{% endhighlight %}

Let's install a couple of packages and their dependencies:

{% highlight R %}
options(download.file.method = "wget")
pkgnames <- c("Rcpp", "tools", "methods", "drat")
install.packages(pkgnames, dependencies = TRUE, repos="http://cran.rstudio.com/")
{% endhighlight %}

The following packages also need to be installed, but without dependencies:

{% highlight R %}
pkgnames <- c("stringr", "evaluate", "knitr", "magrittr")
install.packages(pkgnames, dependencies = FALSE, repos="http://cran.rstudio.com/")
{% endhighlight %}

### Step 1: Install swirl

Open R and type the following into the console:

{% highlight r %}
install.packages("swirl")
{% endhighlight %}

### Step 2: Install swirl course

{% highlight r %}
library("swirl")
install_from_swirl("R Programming Alt")
{% endhighlight %}

### Step 3: Start swirl

This is the only step that you will repeat every time you want to run swirl. First, you will load the package using the library() function. Then you will call the function that starts the magic! Type the following, pressing Enter after each line:

{% highlight r %}
swirl()
{% endhighlight %}

## Running Swirl

Type in your name when prompted.

Run course 2: R Programming Alt.

Please complete all 12 lessons:

1. Basic Building Blocks
2. Sequences of Numbers
3. Vectors
4. Missing Values
5. Subsetting Vectors      
6. Matrices and Data Frames
7. Logic
8. lapply and sapply
9. vapply and tapply
10. Looking at Data         
11. Simulation
12. Dates and Times

When you are done with a module, it will ask: "Would you like to inform someone about your successful completion of this lesson?".

* Type 2, Yes.
* Enter in your full name.
* Email me at naomi dot hunsaker at byu dot edu

## Install R on Local Computer

Refer to the CRAN site to download and install R on your local computer: [https://cran.r-project.org](https://cran.r-project.org).
