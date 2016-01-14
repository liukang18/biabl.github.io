---
excerpt_separator: "<!--more-->"
layout: post
author: naomi
tags: 
  - ANTs
  - antsRegistrationSynQuick.sh
  - code improvement
published: true
title: Improved antsRegistrationSynQuick.sh
---

Given our dataset, improvement of antsRegistrationSynQuick.sh was needed. Particularly when other scripts all antsRegistrationSynQuick.sh (e.g., antsJointLabelFusion.sh). The old antsRegistrationSynQuick.sh code was as follows:

{% highlight bash %}
##############################
#
# Construct mapping stages
#
##############################

RIGIDCONVERGENCE="[1000x500x250x0,1e-6,10]"
RIGIDSHRINKFACTORS="8x4x2x1"
RIGIDSMOOTHINGSIGMAS="3x2x1x0vox"

AFFINECONVERGENCE="[1000x500x250x0,1e-6,10]"
AFFINESHRINKFACTORS="8x4x2x1"
AFFINESMOOTHINGSIGMAS="3x2x1x0vox"

SYNCONVERGENCE="[100x70x50x0,1e-6,10]"
SYNSHRINKFACTORS="8x4x2x1"
SYNSMOOTHINGSIGMAS="3x2x1x0vox"

if [[ $ISLARGEIMAGE -eq 1 ]];
  then
    RIGIDCONVERGENCE="[1000x500x250x0,1e-6,10]"
    RIGIDSHRINKFACTORS="12x8x4x2"
    RIGIDSMOOTHINGSIGMAS="4x3x2x1vox"

    AFFINECONVERGENCE="[1000x500x250x0,1e-6,10]"
    AFFINESHRINKFACTORS="12x8x4x2"
    AFFINESMOOTHINGSIGMAS="4x3x2x1vox"

    SYNCONVERGENCE="[100x100x70x50x0,1e-6,10]"
    SYNSHRINKFACTORS="10x6x4x2x1"
    SYNSMOOTHINGSIGMAS="5x3x2x1x0vox"
  fi

tx=Rigid
if [[ $TRANSFORMTYPE == 't' ]] ; then
  tx=Translation
fi

RIGIDSTAGE="--initial-moving-transform [${FIXEDIMAGES[0]},${MOVINGIMAGES[0]},1] \
            --transform ${tx}[0.1] \
            --metric MI[${FIXEDIMAGES[0]},${MOVINGIMAGES[0]},1,32,Regular,0.25] \
            --convergence $RIGIDCONVERGENCE \
            --shrink-factors $RIGIDSHRINKFACTORS \
            --smoothing-sigmas $RIGIDSMOOTHINGSIGMAS"

AFFINESTAGE="--transform Affine[0.1] \
             --metric MI[${FIXEDIMAGES[0]},${MOVINGIMAGES[0]},1,32,Regular,0.25] \
             --convergence $AFFINECONVERGENCE \
             --shrink-factors $AFFINESHRINKFACTORS \
             --smoothing-sigmas $AFFINESMOOTHINGSIGMAS"

SYNMETRICS=''
for(( i=0; i<${#FIXEDIMAGES[@]}; i++ ))
  do
    SYNMETRICS="$SYNMETRICS --metric MI[${FIXEDIMAGES[$i]},${MOVINGIMAGES[$i]},1,${NUMBEROFBINS}]"
  done

SYNSTAGE="${SYNMETRICS} \
          --convergence $SYNCONVERGENCE \
          --shrink-factors $SYNSHRINKFACTORS \
          --smoothing-sigmas $SYNSMOOTHINGSIGMAS"
{% endhighlight %}

The new parameters are as follows:

{% highlight bash %}
##############################
#
# Construct mapping stages
#
##############################

RIGIDCONVERGENCE="[1000x500x250x0,1e-6,10]"
RIGIDSHRINKFACTORS="6x4x2x1"
RIGIDSMOOTHINGSIGMAS="4x2x1x0vox"

AFFINECONVERGENCE="[1000x500x250x0,1e-6,10]"
AFFINESHRINKFACTORS="6x4x2x1"
AFFINESMOOTHINGSIGMAS="4x2x1x0vox"

SYNCONVERGENCE="[100x100x70x20,1e-10,10]"
SYNSHRINKFACTORS="6x4x2x1"
SYNSMOOTHINGSIGMAS="3x2x1x0vox"

if [[ $ISLARGEIMAGE -eq 1 ]];
  then
    RIGIDCONVERGENCE="[1000x500x250x0,1e-6,10]"
    RIGIDSHRINKFACTORS="12x8x4x2"
    RIGIDSMOOTHINGSIGMAS="4x3x2x1vox"

    AFFINECONVERGENCE="[1000x500x250x0,1e-6,10]"
    AFFINESHRINKFACTORS="12x8x4x2"
    AFFINESMOOTHINGSIGMAS="4x3x2x1vox"

    SYNCONVERGENCE="[100x100x70x50x0,1e-10,10]"
    SYNSHRINKFACTORS="10x6x4x2x1"
    SYNSMOOTHINGSIGMAS="5x3x2x1x0vox"
  fi

tx=Rigid
if [[ $TRANSFORMTYPE == 't' ]] ; then
  tx=Translation
fi

RIGIDSTAGE="--initial-moving-transform [${FIXEDIMAGES[0]},${MOVINGIMAGES[0]},1] \
            --transform ${tx}[0.1] \
            --metric MI[${FIXEDIMAGES[0]},${MOVINGIMAGES[0]},1,32,Regular,0.25] \
            --convergence $RIGIDCONVERGENCE \
            --shrink-factors $RIGIDSHRINKFACTORS \
            --smoothing-sigmas $RIGIDSMOOTHINGSIGMAS"

AFFINESTAGE="--transform Affine[0.1] \
             --metric MI[${FIXEDIMAGES[0]},${MOVINGIMAGES[0]},1,32,Regular,0.25] \
             --convergence $AFFINECONVERGENCE \
             --shrink-factors $AFFINESHRINKFACTORS \
             --smoothing-sigmas $AFFINESMOOTHINGSIGMAS"

SYNMETRICS=''
for(( i=0; i<${#FIXEDIMAGES[@]}; i++ ))
  do
    SYNMETRICS="$SYNMETRICS --metric CC[${FIXEDIMAGES[$i]},${MOVINGIMAGES[$i]},1,4]"
  done

SYNSTAGE="${SYNMETRICS} \
          --convergence $SYNCONVERGENCE \
          --shrink-factors $SYNSHRINKFACTORS \
          --smoothing-sigmas $SYNSMOOTHINGSIGMAS"
{% endhighlight %}

Further testing is needed to evaluate when large lesions are present.