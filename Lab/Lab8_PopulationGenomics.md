## Lab 8: Population genomics 

For today's lab we will be doing some rudimentary analyses of population genomics with some very new data. I got these data from a sequencing facility less than a week ago, so I have no idea what they will tell us. It is a mystery!!!! 

### Background information

My work deals a lot with the evolution and maintenance of mimicry in this really cool poison frog that lives in Peru. It is called _Ranitomeya imitator_, or the mimic poison frog (because it mimics other species, obviously). There are 4 mimetic morphs of this species.

![Mimetic morphs of Ranitomeya imitator](https://github.com/AdamStuckert/Gen711/blob/master/Lab/Files/imitator_morphs.png)

Because of it's very interesting evolutionary history, we are very interested in the genomics of coloration and toxicity in this species. Between these pure mimetic morphs are hybrid zones or clinal zones in which the frogs are super phenotypically variable because there seems to be some gene flow between morphs. In 2017 I sampled along one of the hybrid zones between the orange banded morph (top left frog) and the yellow striped morph (top right frog). I sampled 2 pure yellow morph populations, 2 pure orange morph populations, and 2 populations in the hybrid zone. 

![Mimetic morphs of Ranitomeya imitator](https://github.com/AdamStuckert/Gen711/blob/master/Lab/Files/combined_map_figure.png)

For the purposes of this lab, what I've done is selected 5 random individuals from the most distant yellow and orange morph populations. I then subsampled the data down to XX random reads for each individual. I did this just so you all can actually run this in a reasonable time frame (you are welcome!).

OK, background over, time to do some population genomics.

**We will do this lab on Ron.**  First up, download the data.

```bash
# download data
mkdir reads
cd reads
svn checkout https://github.com/AdamStuckert/Gen711/trunk/Lab/Files/RAD_data
cd ..
```

**Question 1: ** How many reads are they, and how many reads do they each have?

There are a few programs that people use to analyze RADseq data, most notably Stacks and ipyrad. For this lab we will be using Stacks. What Stacks does is assemble data from reads into "stacks" of reads that align (hence the name). Conveniently, Stacks can run both to a genome as well as produce these stacks de novo. So you can utilize extant genomic resources for your species of interest, but if those don't exist you can still examine RADseq data in Stacks. 

There is a conda environment for Stacks on Ron. **Question 2 :** What is the name of this environment?



The first step in Stacks is to demultiplex samples + make sure the appropriate restriction enzyme cut site exists. This will drop any reads that are inappropriately attributed to a sample/don't have a cut site. Our reads were demultiplexed by the sequencing core facility, so we don't need to demultiplex, but we do need to check and verify that our cut sites exist for each read. Stacks has an executable for this called `process_radtags`. 

```bash
# We want to put processed reads somewhere:
mkdir processed_reads
# check for cut sites
process_radtags -p ./reads/ --paired  -i gzfastq -o ./processed_reads/ --renz_1 sbfI --renz_2 claI 
```

**Question 3:** What do the parameters `--renz_1` and `--renz_2` refer to? Why are they important for this process?


Stacks can calculate a variety of population genetics metrics between populations. In order to do this we have to specify populations. In this case we have 2 populations of frogs, each of which represents a color morph of our poison frog. Each read is named in a way to give us lots of information about the sample, including year collected, locality, and individual number. **Question 4:** What format are reads in, and if you had to guess what do you think the different parts of the read names refer to?

In order for Stacks to calculate population metrics we have to give it information about which population each sample belongs to. So, lets make a population map with our data. Our data come from two populations that refer to two color morphs. We will specify them as the color morph instead of population, becuase that is ultimately what we (read: Adam) cares about here. Population information.

SA == Sauce == orange banded morph
PO == Pongo == yellow striped morph



```bash
# make the population list...
inds=$(basename -a processed_reads/*R1_001.1.fq | sed "s/_R1_001.1.fq//g")

for ind in $inds
do
pop=$(echo $ind | cut -d - -f2 | sed "s/PO/yellow-striped/g" | sed "s/SA/orange-banded/g")
printf "%s\t%s\n" "$ind" "$pop" >> poplist.tab
done
```

The above is a for loop. These are really, really common in bioinformatics as they are a really convenient way to cycle through a whole bunch of different files in a systematic way. If you continue doing genomics/bioinformatics after the class, I strongly suggest spending some time to learn for loops.

We now have a population map. **Question 5:** What is the content of your population map? Paste in the output of printing it to your screen.

Next up: run Stacks! This program is super nice and integrates all the steps into a single command. In this case we are going to run a de novo Stacks assembly. We actually have a brand new genome for this species, but that is extra steps for you that will take some time! So, we will skip that and run de novo.


```bash
mkdir stacks_out
denovo_map.pl -T 24 -M 6 -o ./stacks_out/ --samples ./processed_reads/ --popmap ./poplist.tab --paired
```

**Question 6: * What happened??? Paste in your output.

If I am being honest, I mostly made you do that because I am petty. I had to figure out what was going on, so you had to at least see what happened.

SO I GUESS WE NOW HAVE TO FIX THE DAMN READ NAMES. FINE.

First off, it makes a bunch of empty fastq files. Just delete those `rm processed_reads/*fastq`. Now a bit of code to rename all the gzipped files.

```bash
cd processed_reads/
inds=$(ls *R1_001.1.fq.gz | sed "s/_R1_001.1.fq.gz//g")

for ind in $inds
do
mv "$ind"_R1_001.1.fq.gz "$ind".1.fq.gz 
mv "$ind"_R2_001.2.fq.gz "$ind".2.fq.gz 
done
```

Now rerun the de novo pipeline (remember where you are in your system).


```bash
denovo_map.pl -T 24 -M 6 -o ./stacks_out/ --samples ./processed_reads/ --popmap ./poplist.tab --paired
```

We are probably interested in the overall difference between populations. How different are they in the overall genome? One way we examine this is the fixation index, which we can calculate via the population commmand in stacks. Including teh `--fstats` flag will give us some fixation index output.

```bash
populations -P ./stacks_out -t 24 -M ./poplist.tab --fstats
```

**Question 7:** What is the Fst between populations in this study?

**Question 8:** What do you think this means about these two color morphs? Are they genetically similar or dissimilar? Why do you think this? Note: you may have to watch the pop gen lectures and/or read up on Fst values in order to answer this!

**Question 9:** Lastly, Ron has limited space and is a teaching resource. Please be good stewards and clean up by removing all the reads in your folder. Post a screenshot which shows the contents of your `reads` and `processed_reads` directories.
