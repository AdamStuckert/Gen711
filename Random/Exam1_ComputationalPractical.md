## Exam 1 lab practical


For this practical we are going to broadly cover some of the materials and techniques that we have covered in lab. This should be somewhat challenging, but you should be able to complete it within the time frame of.

very important note: We have created an image for you with all of the requisite software. **MAKE SURE YOU OPEN THE CORRECT INSTANCE OF CORRECT SIZE.** 

```
INSTANCE: GEN711_exam1practical
SIZE: m1.xlarge


Remember that to connect to your instance you will need to use the command `ssh -i jetkey USERNAME@INSTANCEIPADDRESS`. Obvioulsy fill in your username + ip address accordingly.

Also critical, we have installed most things in a conda environment. You will need to activate this environment. 

**Question 1:** what is the command you used to activate this environment (hint, it is not the environment called `base`).

There is a slight chance that something weird will happen with the conda activation. If this happens, scroll to the very end for instructions (and feel free to ask us for help with this).

You are working on an organism with a reference genome. However, you don't think the genome is phenomenal. Part of your work involved fairly deep PacBio sequencing of the organism, so you figured you should see if you can assemble a genome that is better.

```bash
wget http://gembox.cbcb.umd.edu/mhap/raw/ecoli_p6_25x.filtered.fastq -O PacBioData.fastq
```

We will assemble this genome with the assembler `wtdbg2`. You will need to specify the things that are denoted by a `$` to start with.

```bash
wtdbg2 -x rs -g 4.6m -t 24 -i $InputData -fo wtdbg2out
wtpoa-cns -t 24 -i wtdbg2out.ctg.lay.gz -fo $GenomeAssembly
```

I want you to assess your genome quality using quast and BUSCO. Both are installed in this image.

**Question 2:** What is the full path to the quast installation?

**Question 3:** What is the command you used to run quast on your genome? Hint, supplying quast with a reference genome to compare to your assembly is optional.

**Question 4:** What is your N50?

**Question 3:** What is the command you used to run BUSCO on your genome? Use the bacteria_odb10 lineage for this analysis.

**Question 6:** What is your BUSCO score?


Great work, you've assembled and asseessed your genome! **Congratulations!** You did the thing.



**Question 7:** Based off of these data, and the data from the current reference genome (pictured in the exam document), which genome do you think you should use moving forward and why?


**Delete your instance after completion! There is no reason to keep it open after completing this.**



### If you had issues with conda activation

You will need to do this:

```bash
conda init bash
exit
# log back in with your jetkey
conda deactivate
# then conda activate the correct environment
```

Please ask questions about this if it happens to you!
