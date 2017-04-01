---
excerpt_separator: <!-- more -->
layout: post
published: true
title: Installing Programs on MacBook Pro Running macOS Sierra
author: naomihunsaker
tags:
  - 'tips '
---
My current system is a MacBook Pro (Retina, 15-inch, Mid 2014) with 2.5 GHz Intel Core i7 processor and 16 GB of memory. I am currently running macOS Sierra (Version 10.12.4). Here's the code and instructions for how to install all the current programs. I will also post this page under tutorials!

<!-- more -->

## TextWrangler

Install and download from the Mac App Store. Simple!

## Homebrew

Open a new terminal window and type:

```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

This process should install Command Line Tools for Xcode, but here's the full output from the installation:

```sh
==> This script will install:
/usr/local/bin/brew
/usr/local/share/doc/homebrew
/usr/local/share/man/man1/brew.1
/usr/local/share/zsh/site-functions/_brew
/usr/local/etc/bash_completion.d/brew
/usr/local/Homebrew
==> The following new directories will be created:
/usr/local/Cellar
/usr/local/Homebrew
/usr/local/Frameworks
/usr/local/bin
/usr/local/etc
/usr/local/include
/usr/local/lib
/usr/local/opt
/usr/local/sbin
/usr/local/share
/usr/local/share/zsh
/usr/local/share/zsh/site-functions
/usr/local/var

Press RETURN to continue or any other key to abort
==> /usr/bin/sudo /bin/mkdir -p /usr/local/Cellar /usr/local/Homebrew /usr/local/Frameworks /usr/local/bin /usr/local/etc /usr/local/include /usr/local/lib /usr/local/opt /usr/local/sbin /usr/local/share /usr/local/share/zsh /usr/local/share/zsh/site-functions /usr/local/var
Password:
==> /usr/bin/sudo /bin/chmod g+rwx /usr/local/Cellar /usr/local/Homebrew /usr/local/Frameworks /usr/local/bin /usr/local/etc /usr/local/include /usr/local/lib /usr/local/opt /usr/local/sbin /usr/local/share /usr/local/share/zsh /usr/local/share/zsh/site-functions /usr/local/var
==> /usr/bin/sudo /bin/chmod 755 /usr/local/share/zsh /usr/local/share/zsh/site-functions
==> /usr/bin/sudo /usr/sbin/chown naomihunsaker /usr/local/Cellar /usr/local/Homebrew /usr/local/Frameworks /usr/local/bin /usr/local/etc /usr/local/include /usr/local/lib /usr/local/opt /usr/local/sbin /usr/local/share /usr/local/share/zsh /usr/local/share/zsh/site-functions /usr/local/var
==> /usr/bin/sudo /usr/bin/chgrp admin /usr/local/Cellar /usr/local/Homebrew /usr/local/Frameworks /usr/local/bin /usr/local/etc /usr/local/include /usr/local/lib /usr/local/opt /usr/local/sbin /usr/local/share /usr/local/share/zsh /usr/local/share/zsh/site-functions /usr/local/var
==> /usr/bin/sudo /bin/mkdir -p /Users/naomihunsaker/Library/Caches/Homebrew
==> /usr/bin/sudo /bin/chmod g+rwx /Users/naomihunsaker/Library/Caches/Homebrew
==> /usr/bin/sudo /usr/sbin/chown naomihunsaker /Users/naomihunsaker/Library/Caches/Homebrew
==> /usr/bin/sudo /bin/mkdir -p /Library/Caches/Homebrew
==> /usr/bin/sudo /bin/chmod g+rwx /Library/Caches/Homebrew
==> /usr/bin/sudo /usr/sbin/chown naomihunsaker /Library/Caches/Homebrew
==> Searching online for the Command Line Tools
==> /usr/bin/sudo /usr/bin/touch /tmp/.com.apple.dt.CommandLineTools.installondemand.in-progress
==> Installing Command Line Tools (macOS Sierra version 10.12) for Xcode-8.3
==> /usr/bin/sudo /usr/sbin/softwareupdate -i Command\ Line\ Tools\ (macOS\ Sierra\ version\ 10.12)\ for\ Xcode-8.3
Software Update Tool


Downloading Command Line Tools (macOS Sierra version 10.12) for Xcode
Downloaded Command Line Tools (macOS Sierra version 10.12) for Xcode
Installing Command Line Tools (macOS Sierra version 10.12) for Xcode
Done with Command Line Tools (macOS Sierra version 10.12) for Xcode
Done.
==> /usr/bin/sudo /bin/rm -f /tmp/.com.apple.dt.CommandLineTools.installondemand.in-progress
==> /usr/bin/sudo /usr/bin/xcode-select --switch /Library/Developer/CommandLineTools
==> Downloading and installing Homebrew...
remote: Counting objects: 5669, done.
remote: Compressing objects: 100% (3533/3533), done.
remote: Total 5669 (delta 3141), reused 3746 (delta 1959), pack-reused 0
Receiving objects: 100% (5669/5669), 3.35 MiB | 3.29 MiB/s, done.
Resolving deltas: 100% (3141/3141), done.
From https://github.com/Homebrew/brew
 * [new branch]      master     -> origin/master
 * [new tag]         0.1        -> 0.1
 * [new tag]         0.2        -> 0.2
 * [new tag]         0.3        -> 0.3
 * [new tag]         0.4        -> 0.4
 * [new tag]         0.5        -> 0.5
 * [new tag]         0.6        -> 0.6
 * [new tag]         0.7        -> 0.7
 * [new tag]         0.7.1      -> 0.7.1
 * [new tag]         0.8        -> 0.8
 * [new tag]         0.8.1      -> 0.8.1
 * [new tag]         0.9        -> 0.9
 * [new tag]         0.9.1      -> 0.9.1
 * [new tag]         0.9.2      -> 0.9.2
 * [new tag]         0.9.3      -> 0.9.3
 * [new tag]         0.9.4      -> 0.9.4
 * [new tag]         0.9.5      -> 0.9.5
 * [new tag]         0.9.8      -> 0.9.8
 * [new tag]         0.9.9      -> 0.9.9
 * [new tag]         1.0.0      -> 1.0.0
 * [new tag]         1.0.1      -> 1.0.1
 * [new tag]         1.0.2      -> 1.0.2
 * [new tag]         1.0.3      -> 1.0.3
 * [new tag]         1.0.4      -> 1.0.4
 * [new tag]         1.0.5      -> 1.0.5
 * [new tag]         1.0.6      -> 1.0.6
 * [new tag]         1.0.7      -> 1.0.7
 * [new tag]         1.0.8      -> 1.0.8
 * [new tag]         1.0.9      -> 1.0.9
 * [new tag]         1.1.0      -> 1.1.0
 * [new tag]         1.1.1      -> 1.1.1
 * [new tag]         1.1.10     -> 1.1.10
 * [new tag]         1.1.11     -> 1.1.11
 * [new tag]         1.1.2      -> 1.1.2
 * [new tag]         1.1.3      -> 1.1.3
 * [new tag]         1.1.4      -> 1.1.4
 * [new tag]         1.1.5      -> 1.1.5
 * [new tag]         1.1.6      -> 1.1.6
 * [new tag]         1.1.7      -> 1.1.7
 * [new tag]         1.1.8      -> 1.1.8
 * [new tag]         1.1.9      -> 1.1.9
HEAD is now at e30eeb3 Merge pull request #2426 from Zverik/osmium
==> Tapping homebrew/core
Cloning into '/usr/local/Homebrew/Library/Taps/homebrew/homebrew-core'...
remote: Counting objects: 4278, done.
remote: Compressing objects: 100% (4093/4093), done.
remote: Total 4278 (delta 39), reused 438 (delta 16), pack-reused 0
Receiving objects: 100% (4278/4278), 3.40 MiB | 4.37 MiB/s, done.
Resolving deltas: 100% (39/39), done.
Tapped 4096 formulae (4,315 files, 10.7MB)
==> Cleaning up /Library/Caches/Homebrew...
==> Migrating /Library/Caches/Homebrew to /Users/naomihunsaker/Library/Caches/Ho
==> Deleting /Library/Caches/Homebrew...
Already up-to-date.
==> Installation successful!

==> Homebrew has enabled anonymous aggregate user behaviour analytics.
Read the analytics documentation (and how to opt-out) here:
  http://docs.brew.sh/Analytics.html

==> Next steps:
- Run `brew help` to get started
- Further documentation: 
    http://docs.brew.sh
```

## wget

Now that you have homebrew installed you can install wget. In your terminal window type:

```bash
brew install wget
```

The installation only takes a few seconds:

```sh
==> Installing dependencies for wget: openssl
==> Installing wget dependency: openssl
==> Downloading https://homebrew.bintray.com/bottles/openssl-1.0.2k.sierra.bottl
######################################################################## 100.0%
==> Pouring openssl-1.0.2k.sierra.bottle.tar.gz
==> Using the sandbox
==> Caveats
A CA file has been bootstrapped using certificates from the SystemRoots
keychain. To add additional certificates (e.g. the certificates added in
the System keychain), place .pem files in
  /usr/local/etc/openssl/certs

and run
  /usr/local/opt/openssl/bin/c_rehash

This formula is keg-only, which means it was not symlinked into /usr/local.

Apple has deprecated use of OpenSSL in favor of its own TLS and crypto libraries

If you need to have this software first in your PATH run:
  echo 'export PATH="/usr/local/opt/openssl/bin:$PATH"' >> ~/.bash_profile

For compilers to find this software you may need to set:
    LDFLAGS:  -L/usr/local/opt/openssl/lib
    CPPFLAGS: -I/usr/local/opt/openssl/include

==> Summary
🍺  /usr/local/Cellar/openssl/1.0.2k: 1,696 files, 12MB
==> Installing wget 
==> Downloading https://homebrew.bintray.com/bottles/wget-1.19.1.sierra.bottle.t
######################################################################## 100.0%
==> Pouring wget-1.19.1.sierra.bottle.tar.gz
🍺  /usr/local/Cellar/wget/1.19.1: 10 files, 1.6MB
```

## Xcode

Install and download from the Mac App Store. Simple (fingers crossed)!

## cmake

Next install cmake. In your terminal window type:

```
brew install cmake
```

Again the process should only take a few seconds:

```sh
==> Downloading https://homebrew.bintray.com/bottles/cmake-3.7.2.sierra.bottle.tar.gz
==> Pouring cmake-3.7.2.sierra.bottle.tar.gz
==> Caveats
Emacs Lisp files have been installed to:
  /usr/local/share/emacs/site-lisp/cmake
==> Summary
🍺  /usr/local/Cellar/cmake/3.7.2: 2,143 files, 29MB
```

## Multi-image Analysis GUI (MANGO)

Go to the website and download the latest version: [http://ric.uthscsa.edu/mango/mango.html](http://ric.uthscsa.edu/mango/mango.html). Double-click the installer located in your Download folder.

## R for Mac OS X

Download the latest version of R from [here](https://cran.r-project.org/bin/macosx/). Double-click the installer located in your Download folder.

## XQuartz

Download the XQuartz installer [here](https://www.xquartz.org). Double-click the installer located in your Download folder.

## Convert3D

Download the latest version from [here](http://www.itksnap.org/pmwiki/pmwiki.php?n=Downloads.C3D). Double-click the installer located in your Download folder. Drag and drop the Convert3DGUI into your Applications folder.

In order to call the commands of Convert3D directly, we need to add the path in our ~/.bash_profile.

```bash
export PATH="/Applications/Convert3DGUI.app/Contents/bin/:$PATH"
```

Once you've added to the following to your ~/.bash_profile, you can check the Convert3D program:

```bash
c3d -h
```

The help file is long and extensive:

```sh
Image Input/Output and Information: 
    -dicom-series-list              : List image series in a DICOM directory
    -dicom-series-read              : Read a DICOM image series
    -info                           : Display brief image information        
    -info-full                      : Display verbose image information        
    -mcs, -multicomponent-split     : Enable splitting of multi-component images on read
    -nomcs, -no-multicomponent-split: Disable splitting of multi-component images on read
    -o                              : Output (write) last image on the stack to image file    
    -omc, -output-multicomponent    : Output multiple images to single file
    -oo                             : Output multiple images to multiple files 
Stack Manipulation and Flow Control: 
    -accum, -endaccum               : Accumulate operations over all images
    -as                             : Assign image at the end of the stack to a variable
    -clear                          : Clear the image stack
    -foreach, -endfor               : Loop commands over all images on the stack
    -insert                         : Insert image anywhere in the stack
    -pop                            : Remove last image from the stack
    -popas                          : Remove last image from the stack and assign to variable
    -push                           : Place variable at the end of the stack
    -reorder                        : Rearrange images on the stack
    -dup                            : Duplicate the last image on the stack
Voxelwise Calculations: 
    -add                            : Voxelwise image addition
    -atan2                          : Voxelwise angle from sine and cosine
    -clip                           : Clip image intensity to range
    -cos                            : Voxelwise cosine 
    -divide                         : Voxelwise image division    
    -exp                            : Voxelwise natural exponent
    -erf                            : Standard error function
    -log, -ln                       : Voxelwise natural logarithm
    -log10                          : Voxelwise base 10 logarithm
    -max                            : Voxel-wise maximum of two images
    -min                            : Voxel-wise minimum of two images
    -mean                           : Mean of all images on the stack    
    -multiply, -times               : Multiply two images
    -reciprocal                     : Image voxelwise reciprocal 
    -replace                        : Replace intensities in image
    -rgb2hsv                        : Convert RGB image to HSV image
    -rms                            : Voxelwise vector norm
    -scale                          : Scale intensity by constant factor
    -shift                          : Shift image intensity by constant
    -sin                            : Sine
    -sqrt                           : Take square root of image
    -stretch                        : Stretch image intensities linearly
    -thresh, -threshold             : Binary thresholding
    -voxel-sum                      : Print sum of all voxel intensities
    -voxel-integral                 : Print volume integral of all voxel intensities
    -wsum, -weighted-sum            : Weighed sum of images with constant weights
    -wsv, -weighed-sum-voxelwise    : Weighed sum of images with spatially varying weights
Image Header Manipulation: 
    -copy-transform                 : Copy header information 
    -mbb, -match-bounding-box       : Match bounding box of one image to another
    -orient                         : Change image orientation
    -origin                         : Set image origin
    -origin-voxel                   : Assign image origin to a voxel
    -set-sform                      : Set the transform to physical space
    -spacing                        : Set voxel spacing
Image Processing: 
    -ad, -anisotropic-diffusion     : Perona-Malik anisotropic diffusion filter
    -biascorr                       : Automatic MRI bias field correction
    -alm, -align-landmarks          : Align images based on landmark matching
    -binarize                       : Convert image to binary
    -canny                          : Canny edge detector
    -centroid                       : Report centroid of foreground voxels
    -color-map, -colormap           : Convert scalar image to RGB using color map    
    -conv                           : Convolution
    -comp, -connected-components    : Compute connected components
    -cmv, -coordinate-map-voxel     : Generate voxel coordinate maps (voxel units)
    -cmp, -coordinate-map-physical  : Generate voxel coordinate maps (voxel units)
    -create                         : Generate blank image
    -dilate                         : Binary dilation
    -erode                          : Binary erosion
    -fft                            : Fast Fourier transform
    -flip                           : Flip image around an axis    
    -glm                            : General linear model    
    -grad, -gradient                : Image gradient
    -hesseig, -hessian-eigenvalues  : Compute eigenvalues of the Hessian matrix
    -hessobj, -hessian-objectness   : Hessian objectness filter
    -holefill                       : Fill holes in binary image
    -lstat, -label-statistics       : Display segmentation volumes and intensity statistics
    -lms, -landmarks-to-spheres     : Generate image of spheres from landmark data
    -laplacian, -laplace            : Laplacian filter
    -levelset                       : Level set segmentation
    -mf, -mean-filter               : Mean filter
    -median, -median-filter         : Median filter
    -merge                          : Merge images from previous split command   
    -msq, -mean-square              : Compute mean square difference metric
    -mi, -mutual-info               : Compute mutual informaiton metric
    -mmi, -mattes-mutual-info       : Compute mutual informaiton metric
    -ncc, -normalized-cross-correlation: Compute normalized cross-correlation image
    -nmi, -normalized-mutual-info   : Compute mutual informaiton metric
    -ncor, -normalized-correlation  : Compute normalized correlation metric
    -nlw, -normalize-local-window   : Standardize image intensity using local neighborhood
    -oli, -overlay-label-image      : Overlay segmentation image on grayscale image
    -overlap                        : Compute relative overlap between binary images    
    -pad                            : Pad image with constant value
    -pca                            : Principal components analysis of foreground voxels
    -probe                          : Report image intensity at a voxel
    -rank                           : Voxelwise ranking of intensity values
    -rf-train                       : Train Random Forest classifier
    -rf-apply                       : Apply Random Forest classifier 
    -region                         : Extract region from image
    -resample                       : Resample image to new dimensions
    -resample-mm                    : Resample image to new resolution
    -reslice-matrix                 : Resample image using affine transform
    -reslice-itk                    : Resample image using affine transform
    -reslice-identity               : Resample image using identity transform 
    -sdt, -signed-distance-transform: Signed distance transform of a binary image
    -sharpen                        : Sharpen edges in the image
    -slice                          : Extract slices from an image
    -smooth                         : Gaussian smoothing
    -smooth-fast                    : Fast approximate Gaussian smoothing
    -split                          : Split multi-label image into binary images
    -staple                         : STAPLE algorithm to combine segmentations
    -steig, -structure-tensor-eigenvalues: Compute eigenvalues of the structure tensor
    -test-image, -test-probe        : Test condition
    -tile                           : Tile and stack multiple images into one
    -trim                           : Trim background region of image
    -trim-to-size                   : Trim image to given size
    -vote                           : Vote among images on the stack
    -vote-mrf                       : Vote with Markov Random Field regularlization
    -voxreg, -voxelwise-regression  : Regression between two images
    -wrap                           : Wrap (rotate) image 
Options and Parameters: 
    -background                     : Specify background intensity
    -compress, -no-compress         : Enable/disable compression for some image files
    -interpolation                  : Set interpolation mode
    -noround, -round                : Floating point rounding behavior
    -pim, -percent-intensity-mode   : Set behavior of % specifier
    -rf-param-patch                 : Random Forest training patch size
    -rf-param-usexyz                : Random Forest coordinate features
    -rf-param-treedepth             : Random Forest tree depth
    -rf-param-ntrees                : Random Forest forest size
    -spm, -nospm                    : SPM compatibility in Analyze output
    -type                           : Specify pixel type for image output
    -verbose                        : Enable verbose output of commands
Getting help:
    -h                              : List commands
    -h command                      : Print help on command (e.g. -h add)
    -manual                         : Print complete reference manual
```

## dcm2niix


Now we start downloading stuff off of GitHub. With all these unique neuroimaging programs, I find it helpful to create a user specific *Applications* directory. In the terminal window:

```bash
mkdir -p ~/Applications/
```

To install *dcm2nii* type the following into a terminal window:

```bash
cd ~/Applications/
git clone https://github.com/rordenlab/dcm2niix.git
cd dcm2niix
mkdir build && cd build
cmake ../
```

This step should be quick:

```sh
OFF
-- Configuring done
-- Generating done
-- Build files have been written to: /Users/naomihunsaker/Applications/dcm2niix/build
```

When your configuration file is set, you can build the program:

```bash
make
```

Building the program takes on a few minutes:

```sh
Scanning dependencies of target dcm2niix
[ 12%] Building CXX object console/CMakeFiles/dcm2niix.dir/main_console.cpp.o
clang: warning: argument unused during compilation: '-dead_strip' [-Wunused-command-line-argument]
/Users/naomihunsaker/Applications/dcm2niix/console/main_console.cpp:154:29: warning: absolute value
      function 'abs' given an argument of type 'long' but has parameter of type 'int' which may cause
      truncation of value [-Wabsolute-value]
                opts.gzLevel = abs(strtol(argv[i], NULL, 10));
                               ^
/Users/naomihunsaker/Applications/dcm2niix/console/main_console.cpp:154:29: note: use function
      'std::abs' instead
                opts.gzLevel = abs(strtol(argv[i], NULL, 10));
                               ^~~
                               std::abs
/Users/naomihunsaker/Applications/dcm2niix/console/main_console.cpp:154:29: note: include the header
      <cstdlib> or explicitly provide a declaration for 'std::abs'
1 warning generated.
[ 25%] Building CXX object console/CMakeFiles/dcm2niix.dir/nii_dicom.cpp.o
clang: warning: argument unused during compilation: '-dead_strip' [-Wunused-command-line-argument]
[ 37%] Building CXX object console/CMakeFiles/dcm2niix.dir/jpg_0XC3.cpp.o
clang: warning: argument unused during compilation: '-dead_strip' [-Wunused-command-line-argument]
[ 50%] Building CXX object console/CMakeFiles/dcm2niix.dir/ujpeg.cpp.o
clang: warning: argument unused during compilation: '-dead_strip' [-Wunused-command-line-argument]
[ 62%] Building CXX object console/CMakeFiles/dcm2niix.dir/nifti1_io_core.cpp.o
clang: warning: argument unused during compilation: '-dead_strip' [-Wunused-command-line-argument]
[ 75%] Building CXX object console/CMakeFiles/dcm2niix.dir/nii_ortho.cpp.o
clang: warning: argument unused during compilation: '-dead_strip' [-Wunused-command-line-argument]
[ 87%] Building CXX object console/CMakeFiles/dcm2niix.dir/nii_dicom_batch.cpp.o
clang: warning: argument unused during compilation: '-dead_strip' [-Wunused-command-line-argument]
[100%] Linking CXX executable ../../bin/dcm2niix
[100%] Built target dcm2niix
```

You can double check that dcm2niix is installed correctly by typing:

```bash
~/Applications/dcm2niix/bin/dcm2niix
```

The help file for this program is well documented:

```sh
Compression will be faster with /usr/local/bin/pigz
Chris Rorden's dcm2niiX version v1.0.20170314 (64-bit MacOS)
usage: dcm2niix [options] <in_folder>
 Options :
  -1..-9 : gz compression level (1=fastest, 9=smallest)
  -b : BIDS sidecar (y/n, default n)
  -f : filename (%a=antenna  (coil) number, %c=comments, %d=description, %e echo number, %f=folder name, %i ID of patient, %m=manufacturer, %n=name of patient, %p=protocol, %s=series number, %t=time, %u=acquisition number, %z sequence name; default '%f_%p_%t_%s')
  -h : show help
  -i : ignore derived and 2D images (y/n, default n)
  -t : text notes includes private patient details (y/n, default n)
  -m : merge 2D slices from same series regardless of study time, echo, coil, orientation, etc. (y/n, default n)
  -o : output directory (omit to save to input folder)
  -p : Philips precise float (not display) scaling (y/n, default y)
  -s : single file mode, do not convert other images in folder (y/n, default n)
  -t : text notes includes private patient details (y/n, default n)
  -v : verbose (n/y or 0/1/2 [no, yes, logorrheic], default 0)
  -x : crop (y/n, default n)
  -z : gz compress images (y/i/n, default n) [y=pigz, i=internal, n=no]
 Defaults file : /Users/naomihunsaker/.dcm2nii.ini
 Examples :
  dcm2niix /Users/chris/dir
  dcm2niix -o /users/cr/outdir/ -z y ~/dicomdir
  dcm2niix -f mystudy%s ~/dicomdir
  dcm2niix -o "~/dir with spaces/dir" ~/dicomdir
```

## ANTs

Here we will create a *build* directory in our *~//Applications* directory:

```bash
mkdir -p ~/Applications/build
cd ~/Applications/build
```

Download the latest version of ANTs:

```bash
git clone git://github.com/stnava/ANTs.git/
```

The download should not take long and now we are ready to install ANTs:

```
cd ~/Applications/
mkdir ants && cd ants
ccmake ../build/ANTs
```

Press *c* to configure and continue to press *c* until your can press *g* to generate the configuration file. When you exit out of the cmake app type:

```
make
```

This process takes approximately 30 minutes.

When ANTs has installed we need to copy the script files into the right location:

```bash
cp -v ~/Applications/build/ANTs/Scripts/* ~/Applications/ants/bin/
```

Finally, we need to add some text to our ~/.bash_profile.

```bash
# ANTs
export ANTSPATH=/Users/naomihunsaker/Applications/ants/bin/
PATH=${ANTSPATH}:${PATH}
```

You know you have everything installed correctly if you can type the following into your terminal window:

```bash
N4BiasFieldCorrection
```

And you see the following output:

```sh
COMMAND: 
     N4BiasFieldCorrection
          N4 is a variant of the popular N3 (nonparameteric nonuniform normalization) 
          retrospective bias correction algorithm. Based on the assumption that the 
          corruption of the low frequency bias field can be modeled as a convolution of 
          the intensity histogram by a Gaussian, the basic algorithmic protocol is to 
          iterate between deconvolving the intensity histogram by a Gaussian, remapping 
          the intensities, and then spatially smoothing this result by a B-spline modeling 
          of the bias field itself. The modifications from and improvements obtained over 
          the original N3 algorithm are described in the following paper: N. Tustison et 
          al., N4ITK: Improved N3 Bias Correction, IEEE Transactions on Medical Imaging, 
          29(6):1310-1320, June 2010. 

OPTIONS: 
     -d, --image-dimensionality 2/3/4
          This option forces the image to be treated as a specified-dimensional image. If 
          not specified, N4 tries to infer the dimensionality from the input image. 

     -i, --input-image inputImageFilename
          A scalar image is expected as input for bias correction. Since N4 log transforms 
          the intensities, negative values or values close to zero should be processed 
          prior to correction. 

     -x, --mask-image maskImageFilename
          If a mask image is specified, the final bias correction is only performed in the 
          mask region. If a weight image is not specified, only intensity values inside 
          the masked region are used during the execution of the algorithm. If a weight 
          image is specified, only the non-zero weights are used in the execution of the 
          algorithm although the mask region defines where bias correction is performed in 
          the final output. Otherwise bias correction occurs over the entire image domain. 
          See also the option description for the weight image. If a mask image is *not* 
          specified then the entire image region will be used as the mask region. Note 
          that this is different than the N3 implementation which uses the results of Otsu 
          thresholding to define a mask. However, this leads to unknown anatomical regions 
          being included and excluded during the bias correction. 

     -r, --rescale-intensities 0/(1)
          At each iteration, a new intensity mapping is calculated and applied but there 
          is nothing which constrains the new intensity range to be within certain values. 
          The result is that the range can "drift" from the original at each iteration. 
          This option rescales to the [min,max] range of the original image intensities 
          within the user-specified mask. 

     -w, --weight-image weightImageFilename
          The weight image allows the user to perform a relative weighting of specific 
          voxels during the B-spline fitting. For example, some studies have shown that N3 
          performed on white matter segmentations improves performance. If one has a 
          spatial probability map of the white matter, one can use this map to weight the 
          b-spline fitting towards those voxels which are more probabilistically 
          classified as white matter. See also the option description for the mask image. 

     -s, --shrink-factor 1/2/3/(4)/...
          Running N4 on large images can be time consuming. To lessen computation time, 
          the input image can be resampled. The shrink factor, specified as a single 
          integer, describes this resampling. Shrink factors <= 4 are commonly used.Note 
          that the shrink factor is only applied to the first two or three dimensions 
          which we assume are spatial. 

     -c, --convergence [<numberOfIterations=50x50x50x50>,<convergenceThreshold=0.0>]
          Convergence is determined by calculating the coefficient of variation between 
          subsequent iterations. When this value is less than the specified threshold from 
          the previous iteration or the maximum number of iterations is exceeded the 
          program terminates. Multiple resolutions can be specified by using 'x' between 
          the number of iterations at each resolution, e.g. 100x50x50. 

     -b, --bspline-fitting [splineDistance,<splineOrder=3>]
                           [initialMeshResolution,<splineOrder=3>]
          These options describe the b-spline fitting parameters. The initial b-spline 
          mesh at the coarsest resolution is specified either as the number of elements in 
          each dimension, e.g. 2x2x3 for 3-D images, or it can be specified as a single 
          scalar parameter which describes the isotropic sizing of the mesh elements. The 
          latter option is typically preferred. For each subsequent level, the spline 
          distance decreases in half, or equivalently, the number of mesh elements doubles 
          Cubic splines (order = 3) are typically used. The default setting is to employ a 
          single mesh element over the entire domain, i.e., -b [1x1x1,3]. 

     -t, --histogram-sharpening [<FWHM=0.15>,<wienerNoise=0.01>,<numberOfHistogramBins=200>]
          These options describe the histogram sharpening parameters, i.e. the 
          deconvolution step parameters described in the original N3 algorithm. The 
          default values have been shown to work fairly well. 

     -o, --output correctedImage
                  [correctedImage,<biasField>]
          The output consists of the bias corrected version of the input image. 
          Optionally, one can also output the estimated bias field. 

     --version 
          Get Version Information. 

     -v, --verbose (0)/1
          Verbose output. 

     -h 
          Print the help menu (short version). 

     --help 
          Print the help menu. 
```

## FreeSurfer

Download the latest version of FreeSurfer. In your terminal window, type:

```bash
cd ~/Applications/build/
wget ftp://surfer.nmr.mgh.harvard.edu/pub/dist/freesurfer/6.0.0/freesurfer-Darwin-OSX-stable-pub-v6.0.0.dmg
```

My internet speed as I'm doing this is a bit slow, 23.8 Mbps download and 3.22 Mbps upload. The download of FreeSurfer says it'll take approximately 30 minutes. So while ANTs is installing, I'm also downloading FreeSurfer.

When it has finished downloading, double-click the installer in your *~/Applications/build/* folder. The installer will install freesurfer under *Applications*. You will need to add the following lines to your ~/.bash_profile as well:

```bash
# FREESURFER
export FREESURFER_HOME=/Applications/freesurfer
source $FREESURFER_HOME/SetUpFreeSurfer.sh
```

If done correctly, you should see (remember you may need to `source ~/.bash_profile`):

```sh
-------- freesurfer-Darwin-OSX-stable-pub-v6.0.0-2beb96c --------
Setting up environment for FreeSurfer/FS-FAST (and FSL)
FREESURFER_HOME   /Applications/freesurfer
FSFAST_HOME       /Applications/freesurfer/fsfast
FSF_OUTPUT_FORMAT nii.gz
SUBJECTS_DIR      /Applications/freesurfer/subjects
INFO: /Users/naomihunsaker/matlab/startup.m does not exist ... creating
MNI_DIR           /Applications/freesurfer/mni
```

## FSL

The easiest way and probably the best way to install FSL on your computer is to download the fslinstaller.py from [here](https://fsl.fmrib.ox.ac.uk/fsldownloads/fsldownloadmain.html). Once the small file has downloaded, type the following in the terminal window:

```bash
cd ~/Downloads/
python fslinstaller.py
```

The installer asks you some questions:

```sh
--- FSL Installer - Version 2.0.21 ---
[Warning] Some operations of the installer require administative rights, for example installing into the default folder of /usr/local. If your account is an 'Administrator' (you have 'sudo' rights) then you will be prompted for your administrator password when necessary.
[OK] Installer is current
Where would you like to install FSL? [/usr/local]:
```

Just hit *RETURN*: 

```sh
[Warning] Unsupported OS (apple darwin 16.5). This version of the OS has not been fully tested.
Downloading FSL version 5.0.9 (this may take some time)
```

They are NOT kidding. This is a 45 - 60 minute download.

## TORTOISE

I'm still learning how to use this program, but it'll be helpful to install. Create an account [here](https://tortoisedti.nichd.nih.gov/stbb/login.html) and download the latest version of TORTOISE. I am downloading v3.0.0.

Double-click the downloaded tar ball in your Downloads folder. On your Mac, you should be able to automatically unpack the tar ball. When it is unpacked, drag and drop the TORTOISE directory into your *~/Applications/* directory.

You also need to add text to your ~/.bash_profile:

```bash
# TORTOISE
export PATH=/Users/naomihunsaker/Applications/TORTOISE_V3.0.0/DIFFPREPV3/bin/bin:${PATH}
export PATH=/Users/naomihunsaker/Applications/TORTOISE_V3.0.0/DRBUDDIV3/bin:${PATH}
```

## MRtrix3

If you currently do not plan to contribute to the Mrtrix3 code, the most convenient way to install Mrtrix3 on MacOS X is to install it via homebrew. First make sure your homebrew is up-to-date and you have qt5 installed (which takes a few minutes):

```bash
brew update
brew install qt5
```

Next, you'll probably need to setup your command line tools again:

```bash
sudo xcode-select -s /Applications/Xcode.app/Contents/Developer
```

Finally, get the MRtrix3 files and install with brew:

```bash
brew tap MRtrix3/mrtrix3
brew install mrtrix3
```

## ~/.bash_profile

When everything is installed, you should see the following text in your ~/.bash_profile:

```bash
# Convert3D
export PATH="/Applications/Convert3DGUI.app/Contents/bin/:$PATH"

# FreeSurfer
export FREESURFER_HOME=/Applications/freesurfer
source $FREESURFER_HOME/SetUpFreeSurfer.sh

# ANTs
export ANTSPATH=/Users/naomihunsaker/Applications/ants/bin/
PATH=${ANTSPATH}:${PATH}

# FSL Setup
FSLDIR=/usr/local/fsl
PATH=${FSLDIR}/bin:${PATH}
export FSLDIR PATH
. ${FSLDIR}/etc/fslconf/fsl.sh

# TORTOISE
export PATH=/Users/naomihunsaker/Applications/TORTOISE_V3.0.0/DIFFPREPV3/bin/bin:${PATH}
export PATH=/Users/naomihunsaker/Applications/TORTOISE_V3.0.0/DRBUDDIV3/bin:${PATH}
```

## MATLAB

This is the only program that isn't free, so install it if you can. I install MATLAB so I can run VISTASOFT software. I am currently running the latest version R2017a.

### Packages

I have included the follow add-on packages to MATLAB:

* LHON2
* Automated Fiber Quantification (AFQ)
* VISTASOFT
* SPM5
* SPM8

In terminal:

```bash
cd ~/Applications/
git clone https://github.com/shmp0722/LHON2.git LHON2
git clone https://github.com/yeatmanlab/AFQ.git afq
git clone https://github.com/vistalab/vistasoft.git vistasoft
wget http://www.fil.ion.ucl.ac.uk/spm/download/restricted/contradistinction/spm5.zip
unzip spm5.zip && rm spm5.zip
wget http://www.fil.ion.ucl.ac.uk/spm/download/restricted/idyll/spm8.zip
unzip spm8.zip && rm spm8.zip
```

I am anxiously awaiting the release of the [Python version of AFQ](http://yeatmanlab.github.io/pyAFQ/) so I can move away from the MATLAB.