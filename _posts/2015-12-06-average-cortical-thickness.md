---
layout: post
title: Average Cortical Thickness Consideration
author: naomihunsaker
tags: 
  - c3d
  - ANTs
  - antsCorticalThickness.sh
excerpt_separator: "<!-- more -->"
published: true
---

If you were to just take the average of all the voxels in the cortical thickness image, you would be including a lot of extra non-cortical voxels that will GREATLY skew your data!

<!-- more -->

In order to deal with this issue, if you use the gray matter segmentation as a mask, then when you acquire the average thickness, it will be for the cortical regions only. Remember the the human cortex ranges from about 1 to 4mm in thickness with an average around 2.5 to 3mm. 

## Generate Data

In order to extract the average cortical thickness in a single participant:

{% highlight bash %}
cd /path/to/antsCorticalThickness/directory/
~/apps/c3d/bin/c3d BrainSegmentation.nii.gz -threshold 2 2 1 0 -as MASK CorticalThickness.nii.gz -push MASK -lstat
{% endhighlight %}

Let's compare the output if we didn't run this code, but ran the code to just get the average intensity of ALL the voxels:

{% highlight bash %}
~/apps/c3d/bin/c3d CorticalThickness.nii.gz -info-full
Image #1:
  Image Dimensions   : [198, 236, 200]
  Bounding Box       : {[100.999 96.2826 -87.5596], [298.933 332.089 112.618]}
  Voxel Spacing      : [0.999667, 0.99918, 1.00089]
  Intensity Range    : [0, 10.4775]
  Mean Intensity     : 0.271343
{% endhighlight %}

Obvious a cortical thickness of 0.271343 is not valid. With the correct code you can see that the average thickness is actually 4.09824 \\( \pm \\) 1.42453.

If you want to try and run this specifically with our course dataset:

{% highlight bash %}
for subj in $(ls ~/compute/data/); do 
subjDir=~/compute/data/${subj}/
echo $subj >> ~/compute/projects/corticalthickness/data.csv 
~/apps/c3d/bin/c3d \
${subjDir}/antsCorticalThickness/BrainSegmentation.nii.gz \
-threshold 2 2 1 0 \
-as MASK \
${subjDir}/antsCorticalThickness/CorticalThickness.nii.gz \
-push MASK \
-label-statistics >> ~/compute/projects/corticalthickness/data.csv
done
{% endhighlight %}

## Regular Expressions

The file is not perfectly formatted so that it can be easily imported into R or even useful in Excel. Some editing of the file needs to happen and we can use the power of Regular Expressions in Terminal to edit the file. Just make sure to change the username in the first line of the code.

{% highlight bash %}
input="/fslhome/username/compute/projects/corticalthickness/data.csv"
output="${input%.*}_modified.csv"
cd $(dirname $input)
grep -vwE "(Reading #1|LabelID|^(    0))" ${input} | sed -e "s|/|,|" -Ee "s/[ ]+/,/g" > temp.txt
awk '/^[0-9][0-9][0-9][0-9]_[0-9][0-9][0-9][0-9][0-9][0-9]/{for(i=0;i<1;i++)print}' temp.txt > part1.txt
awk '/^,/{print}' temp.txt > part2.txt
sed -i '' -e "s|,1,|,corticalthickness,|" part2.txt 
paste part1.txt part2.txt > ${output}
perl -pi -e 'print "subjectid, label, mean, std, max, min, count, volume (mm^3), extent x, extent y, extent z\n" if($.==1)' ${output}
rm part1.txt
rm part2.txt
rm temp.txt
{% endhighlight %}

Note this code works for the SOBIK dataset. If you have different subject IDs, you'll need to edit the regular expression in line #5. If you need more help figuring out regular expressions visit this website here:

[https://regex101.com/](https://regex101.com/)

Happy Coding!