---
layout: tutorials
title: Learn R
author: naomi
comments: true
date: 2015-12-09
---
       
## Install Swirl

### Step 1: Install R

In order to run swirl, you must have R 3.0.2 or later installed on your computer. If you need to install R, you can do so [here](https://cran.rstudio.com/).

### Step 2: Install swirl

Open R and type the following into the console:

{% highlight r %}
install.packages("swirl")
{% endhighlight %}

### Step 3: Install swirl course

{% highlight r %}
library("swirl")
install_from_swirl("R Programming Alt")
{% endhighlight %}

### Step 4: Start swirl

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

Type 2, Yes.

Enter in your full name.

Email me at naomi dot hunsaker at byu dot edu