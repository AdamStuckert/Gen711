# Lab 3 - Sequence Alignment

In this lab we are going to take some unknown transcripts (quite a few of them) and figure out what they are. We will do this by blasting them against a set of references that have known sequences in them. Then we will take all the sequences that code for a certain gene (some of which we will find from blasting and some of which we will pull out of the known sequences) and do a multiple sequence alignment in MAFFT. We'll visualize the alignment using an online program at the end.

**Question 1** has been postponed until next week!

If you needed to get to your home directory from some other location, this command will get you there. 
> cd

**Question 2:** What other command could you use to return to your home directory from a different location?

### On to the lab itself!

You have just returned from Central America, where you captured (and then, sadly, killed for its RNA...) a never seen before desert rodent. You are interested in knowing how it survives, and start by trying to identify the sodium transport genes, in particular a gene called SCN5A. You've just had the animal's transcriptome sequenced (all of the RNA, after it has been reverse-transcribed into cDNA) and are about to begin the search!

First we need to download the data:
> curl -LO https://s3.amazonaws.com/macmanes_share/transcripts.fasta

It is formatted in a terrible way (lines of sequence are split up onto multiple lines), so we'll run this command to fix the format:
> awk '{if(NR==1) {print $0} else {if($0 ~ /^>/) {print "\n"$0} else {printf $0}}}' transcripts.fasta > fixed_transcripts.fasta

Notice where we put the input file name (transcripts.fasta), and what we called the output file (all the stuff after the ">").

Luckily, there are lots of good rodent genomic resources out there, so you can compare what is in the transcripts to some rodents that already have solid genomes to pull out the things you're interested in for the new rodent. You decide to use a diversity of rodents because you aren't sure which sequences will look most like your mystery desert rodent.

Kangaroo rat!
> curl -LO ftp://ftp.ensembl.org/pub/release-99/fasta/dipodomys_ordii/cdna/Dipodomys_ordii.Dord_2.0.cdna.all.fa.gz

Chinchilla!
> curl -LO ftp://ftp.ensembl.org/pub/release-99/fasta/chinchilla_lanigera/cdna/Chinchilla_lanigera.ChiLan1.0.cdna.all.fa.gz

Mouse!
> curl -LO ftp://ftp.ensembl.org/pub/release-99/fasta/mus_musculus/cdna/Mus_musculus.GRCm38.cdna.all.fa.gz 

Squirrel!
> curl -LO ftp://ftp.ensembl.org/pub/release-99/fasta/ictidomys_tridecemlineatus/cdna/Ictidomys_tridecemlineatus.SpeTri2.0.cdna.all.fa.gz

Look these guys up if you don't know what they are. Aren't rodents adorable?

Now, go ahead and decompress all the files you just downloaded. This is similar to last week, but they are compressed a little bit differently, so we'll use this command instead.
> gzip -d Mus_musculus.GRCm38.cdna.all.fa.gz

**do this for each of the four rodent files you just downloaded**
**also notice that they are named slightly differently now that they are decompressed!**

We want to make the blast database from a combination of these four rodent files, but blast will only take one file! 
**Question 3:** How could we combine these four reference rodent files into a single file that we could use to build a blast database? Looking for a piece of code here.
I named mine "rodent.fasta", so watch out for that if you named yours something different.

Now we have a file that is a combination of the four reference animals, but it is formatted in the same annoying way that the transcript file was, so we'll have to change that one too. Make sure you change the input file name (currently "rodent.fasta") to the name of your combination file, and give it a new output file name (the part that comes after the ">").
> awk '{if(NR==1) {print $0} else {if($0 ~ /^>/) {print "\n"$0} else {printf $0}}}' rodent.fasta > fixed_rodent.fasta


#### BLASTing

Now we can build the database blast will use for searching using the command `makeblastdb`.
Notice the flags just like we've been using with `grep` and other programs. In this case, they are pretty informative, but that isn't always true.
`-in` is where you should put the input file - this is the file you made in question 3, above. Make sure you type in whatever you named your final file in that step.
`-out` is what you want your output files to be called. There will be a few of them, so no need to give them a file extension.
`-dbtype` is the type of database, in our case, nucleotide, but you could also do a protein one if that fit your data or question better.
> makeblastdb -in fixed_rodent.fa -out rodent -dbtype nucl

This will take just a little while (mine took 14.1483 seconds) so be patient.

All that is left to do is blast our queries to our database!
`blastn` is the actual command here. This will call up the blast program
`-db` is where you put your database that you made in the previous step - make sure you put whatever you called it, if it was something different from mine.
`-max_target_seqs` is an option that controls how many blast hits we are shown
`-query` is where we put our unknown sequences to be blasted!
`-outfmt` is a collection of a couple of options, but all of them have to do with how we want the output of blast to be formatted
`evalue` gives the e value cutoff that we want
`-num_threads` is the number of threads we want this command to run on - we can control the speed a bit by having the computer process multiple things at once.
`-out` is the output file name, so pay attention to what you name it if it's different from this one!
> blastn -db rodent -max_target_seqs 1 -query fixed_transcripts.fasta -outfmt '6 qseqid qlen length pident gaps evalue stitle' -evalue 1e-10 -num_threads 6 -out blast.out

We have an output file called `blast.out` so **Question 4:** how do we look at it? Copy your code for your answer. There are multiple correct answers here, but some will be a little more manageable than others.
Take a look at the file. **The first column is the id number of the transcript from the mystery rodent file, and the 6th column is the e value.**
**Question 5:** What is the e value and are higher or lower numbers better?

Oh jeez. The file is super long. How can we find the gene we are actually interested in?
**Question 6:** Find the gene we want (SCN5A) in the output file and copy the command for your answer. *Hint: a `-i` flag might be helpful here.* 

**Question 7:** What is/are the transcript/s from the mystery rodent that had matches to the gene of interest?

#### Pulling out the necessary sequences for the multiple sequence alignment in the next step

Great! Now we can pull out those transcripts from the original transcript file (if you named yours like mine, it will be "fixed_transcripts.fasta"). Keep their headers with them! This is the only way we have of identifying them later.
**Question 8:** Write the code to pull these out and save the results to a file. *Hint: there is a section of the grep man page called "Context line control" that I find fascinating.*

**Question 9:** Pull out the sequences (and their headers) from the file you made the database out of (for me, this is "fixed_rodent.fasta"). Add these to the file you made in Question 8 and paste the command(s) for this question.
Remember when we used `>` last week and things got a little dicey at the end? Make sure you take steps to avoid that this time. Also always a good idea to look at your `grep` results before committing them to a file. I like to pipe mine to `head` just to check things out. 
Multiple ways to accomplish this task, but the `--no-group-separator` flag in `grep` will be useful for most of them.

#### MAFFT

Make sure you change the input (for_mafft.fa) and output (final.aln) file names to match the file that you made/what you want the alignment to be called.
> mafft --reorder --localpair --thread 6 for_mafft.fa > final.aln

Look at this file in some way and copy all of the contents.

Navigate here:
https://www.ebi.ac.uk/Tools/msa/mview/

Select the correct input type in the "Step 1" box and paste in your alignment.
Scroll down to "Step 4" and click "submit".

Now we can visualize the multiple sequence alignment we just made! 
Take a look at the mystery rodent transcripts in this alignment.
**Question 10:** What do you notice about how 18641 compares to the other mystery rodent transcripts? What do you conclude about these transcripts based on this observation?


