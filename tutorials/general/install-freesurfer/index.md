---
layout: tutorials
title: How to Install FreeSurfer on Supercomputer
author: naomi
comments: true
date: 2015-12-09
---

## Let's Do This Thing!

For students needing to use FreeSurfer, log into the supercomputer:

{% highlight bash %}
ssh <username>@ssh.fsl.byu.edu
{% endhighlight %}

Go to your apps directory:

{% highlight bash %}
cd ~/apps/
{% endhighlight %}

Download the latest version of FreeSurfer:

{% highlight bash %}
wget ftp://surfer.nmr.mgh.harvard.edu/pub/dist/freesurfer/5.3.0/freesurfer-Linux-centos6_x86_64-stable-pub-v5.3.0.tar.gz
{% endhighlight %}

Unzip the compressed tar:

{% highlight bash %}
tar -xzvf freesurfer-Linux-centos6_x86_64-stable-pub-v5.3.0.tar.gz
{% endhighlight %}

Set the path to your FreeSurfer directory:

{% highlight bash %}
echo "export FREESURFER_HOME=/fslhome/username/apps/freesurfer" >> ~/.bash_profile
{% endhighlight %}

You will need to register for a license at:

[https://surfer.nmr.mgh.harvard.edu/registration.html](https://surfer.nmr.mgh.harvard.edu/registration.html)

Once you receive your license information, you will need to create a text file in your FreeSurfer directory:

{% highlight bash %}
vi ~/apps/freesurfer/license.txt
{% endhighlight %}

Enter insert mode (type `a`) then copy and paste the license information into the text file. To save hit `ESC` then type `:wq` and hit `RETURN`.

To check to make sure the program works:

{% highlight bash %}
source ~/.bash_profile
~/apps/freesurfer/bin/recon-all
{% endhighlight %}

And you should see this output:

{% highlight bash %}
Required Arguments:
   -subjid <subjid>
   -<process directive>

 Fully-Automated Directive:
  -all           : performs all stages of cortical reconstruction
  -autorecon-all : same as -all

 Manual-Intervention Workflow Directives:
  -autorecon1    : process stages 1-5 (see below)
  -autorecon2    : process stages 6-23
                   after autorecon2, check white surfaces:
                     a. if wm edit was required, then run -autorecon2-wm
                     b. if control points added, then run -autorecon2-cp
                     c. proceed to run -autorecon3
  -autorecon2-cp : process stages 12-23 (uses -f w/ mri_normalize, -keep w/ mri_seg)
  -autorecon2-wm : process stages 15-23
  -autorecon2-inflate1 : 6-18
  -autorecon2-perhemi : tess, sm1, inf1, q, fix, sm2, inf2, finalsurf, ribbon
  -autorecon3    : process stages 24-34
                     if edits made to correct pial, then run -autorecon-pial
  -hemi ?h       : just do lh or rh (default is to do both)

  Autorecon Processing Stages (see -autorecon# flags above):
    1.  Motion Correction and Conform
    2.  NU (Non-Uniform intensity normalization)
    3.  Talairach transform computation
    4.  Intensity Normalization 1
    5.  Skull Strip

    6.  EM Register (linear volumetric registration)
    7.  CA Intensity Normalization
    8.  CA Non-linear Volumetric Registration
    9.  Remove neck
    10. EM Register, with skull
    11. CA Label (Aseg: Volumetric Labeling) and Statistics

    12. Intensity Normalization 2 (start here for control points)
    13. White matter segmentation
    14. Edit WM With ASeg
    15. Fill (start here for wm edits)
    16. Tessellation (begins per-hemisphere operations)
    17. Smooth1
    18. Inflate1
    19. QSphere
    20. Automatic Topology Fixer
    21. White Surfs (start here for brain edits for pial surf)
    22. Smooth2
    23. Inflate2

    24. Spherical Mapping
    25. Spherical Registration
    26. Spherical Registration, Contralater hemisphere
    27. Map average curvature to subject
    28. Cortical Parcellation (Labeling)
    29. Cortical Parcellation Statistics
    30. Pial Surfs
    31. WM/GM Contrast
    32. Cortical Ribbon Mask
    33. Cortical Parcellation mapped to ASeg
    34  Brodmann and exvio EC labels

 Step-wise Directives
  See -help

 Expert Preferences
  -pons-crs C R S : col, row, slice of seed point for pons, used in fill
  -cc-crs C R S : col, row, slice of seed point for corpus callosum, used in fill
  -lh-crs C R S : col, row, slice of seed point for left hemisphere, used in fill
  -rh-crs C R S : col, row, slice of seed point for right hemisphere, used in fill
  -nofill        : do not use the automatic subcort seg to fill
  -watershed cmd : control skull stripping/watershed program
  -wsless : decrease watershed threshold (leaves less skull, but can strip more brain)
  -wsmore : increase watershed threshold (leaves more skull, but can strip less brain)
  -wsatlas : use atlas when skull stripping
  -no-wsatlas : do not use atlas when skull stripping
  -no-wsgcaatlas : do not use GCA atlas when skull stripping
  -wsthresh pct : explicity set watershed threshold
  -wsseed C R S : identify an index (C, R, S) point in the skull
  -nuiterations N : set number of iterations for nu intensity
                    correction (default is 2)
  -norm3diters niters : number of 3d iterations for mri_normalize
  -normmaxgrad maxgrad : max grad (-g) for mri_normalize. Default is 1.
  -norm1-b N : in the _first_ usage of mri_normalize, use control
               point with intensity N below target (default=10.0)
  -norm2-b N : in the _second_ usage of mri_normalize, use control
               point with intensity N below target (default=10.0)
  -norm1-n N : in the _first_ usage of mri_normalize, do N number
               of iterations
  -norm2-n N : in the _second_ usage of mri_normalize, do N number
               of iterations
  -cm           : conform volumes to the min voxel size
  -no-fix-with-ga : do not use genetic algorithm when fixing topology
  -fix-diag-only  : topology fixer runs until ?h.defect_labels files
                    are created, then stops
  -seg-wlo wlo : set wlo value for mri_segment and mris_make_surfaces
  -seg-ghi ghi : set ghi value for mri_segment and mris_make_surfaces
  -nothicken   : pass '-thicken 0' to mri_segment
  -no-ca-align-after : turn off -align-after with mri_ca_register
  -no-ca-align : turn off -align with mri_ca_label
  -deface      : deface subject, written to orig_defaced.mgz

  -expert file     : read-in expert options file
  -xopts-use       : use pre-existing expert options file
  -xopts-clean     : delete pre-existing expert options file
  -xopts-overwrite : overwrite pre-existing expert options file

  -mprage : assume scan parameters are MGH MP-RAGE protocol
  -washu_mprage : assume scan parameters are Wash.U. MP-RAGE protocol.
                  both mprage flags affect mri_normalize and mri_segment,
                  and assumes a darker gm.

  -use-gpu : use GPU versions of mri_em_register, mri_ca_register,
             mris_inflate and mris_sphere

 Notification Files (Optional)
  -waitfor file : wait for file to appear before beginning
  -notify  file : create this file after finishing

 Status and Log files (Optional)
  -log     file : default is scripts/recon-all.log
  -status  file : default is scripts/recon-all-status.log
  -noappend     : start new log and status files instead of appending
  -no-isrunning : do not check whether this subject is currently being processed

 Other Arguments (Optional)
  -sd subjectsdir : specify subjects dir (default env SUBJECTS_DIR)
  -mail username  : mail user when done
  -umask umask    : set unix file permission mask (default 002)
  -grp groupid    : check that current group is alpha groupid
  -onlyversions   : print version of each binary and exit
  -debug          : print out lots of info
  -allowcoredump  : set coredump limit to unlimited
  -dontrun        : do everything but execute each command
  -version        : print version of this script and exit
  -help           : voluminous bits of wisdom
{% endhighlight %}
