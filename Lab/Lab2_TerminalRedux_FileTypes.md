## Intro to terminal continued

For today's lab we will continue our introduction to basic Unix and working in the terminal. We will also begin to work with common file types used in genomics and bioinformatics. You will see many of these file types as you use them throughout your bioinformatical career, whether that lasts for the length of this class or a lifetime.

**Programming note:** _ba dum tss!_

Much of today's material is heavily borrowed from Dr. Chris Balakrishnan's bioinformatics workshop.

First we will begin by learning to download some data from online. There are a variety of ways to do this, but for today we will use `curl`. This is a very useful and important aspect to bioinformatics. I have a compressed folder of files stored on the course's GitHub page. Download this:

```bash
curl LINK
```

Next, view your current directory. Think back to last week to do this. Once you have done this, you should be able to see that you have downloaded a new file! You did this without a user interface or clicking anything! It should be called `Lab2.zip`. Because this is a compressed folder, if you view this with `less Lab2.zip` you should be able to see the contents of the folder. Before accessing any of them, you will have to uncompress this folder first:

```bash
unzip Lab2.zip
```

You can view the contents of your directory--it should now contain all of the same files that you saw when you used `less` to view the zipped directory. We will use these files to start with a bit of a review from last week. Let’s take a peek at the `darwin_chapter.txt` file using the ‘more’ command.

```bash
more darwin_chapter.txt
```

This is the last chapter from Charles Darwin’s last chapter of The Origin of Species’. Press space bar to continue scrolling through the file, type ‘q’ to quit. Now let us say that you want to only look at the first few lines of the file, just to verify it is what you think it is. You can do this by using the ‘head’ command. **Question 1: how many lines does head show?**

```bash
head darwin_chapter.txt
```

To specify the number of lines viewed you can use the -n flag. Show the first 100 lines of the file.

```bash
head -n 100 darwin_chapter.txt 
```

To view the last few lines of a file, use the tail command.

```bash
tail darwin_chapter.txt
```

**Question 2:** How many lines are in this text file?

```bash
wc -l darwin_chapter.txt
```

How many characters are the file? 

```bash
wc -c darwin_chapter.txt
```

Darwin is credited with the theory of natural selection. You can see that Darwin used the word theory quite a bit:

```bash
grep "theory" darwin_chapter.txt
```

Now, you may be interested in counting exactly how many times he says this. Luckily, we have a tool called *piping*, which allows us to link commands together. This is one of the most useful tools in your toolbox. What piping does is say take the output from the first command and use this to run the second command. Since grep outputs a single line from the file each time it finds the requested string, we can then pipe this in to the `wc` command to count the number of times he says grandeur. **Question 3:** How many times does Darwin use the word "theory"?

```bash
grep "theory" darwin_chapter.txt | wc -l
```

**Question 4:** Why might counting the number of times Darwin used theory in this way be problematic and imprecise?

### FASTA files

The FASTA format is a common file format that is used for storing DNA, RNA or Protein sequences. FASTA files begin with a header line starting with ‘>’ that contains text information of the sequence and often an identifier such as the genbank ID (NM_004985.3).  The subsequent lines contain the DNA, RNA or Protein sequence.  Genomic, transcriptomic, and proteomic assemblies will come in this format. FASTA files usually end with the `*.fa` or `*.fasta` extension.

Let’s examine the FASTA file for the KRAS gene
```bash
more kras.fa
```

**Question 5:** how many lines are in this fasta file?

Remember the first day of class when we counted nucleotides? It was awful. We can use commands we have already learned in order to count the total number of nucleotides easily! `grep` has a flag (flags are used to specify parameters to a command) that allows us to find the inverse of a search. We can combine that with `wc` to count the total number of characters using the `-c` or count flag.

```bash
grep -v ">" kras.fa | wc -c
```

**Question 6:** how many total characters are in this fasta file?

**Question 7:** how many total nucleotides are in this fasta file?

### Multi FASTA files

Multi fasta files are the same format as a 'regular' fasta file. However, they contain >1 (usually many more) individual sequences. Each sequence is denoted by the header identifier `>`. The zipped directory you downloaded has a multi fasta file (often referred to as just a fasta file) of protein sequences called `human.pep.fa.gz`. Each character within the sequence is a single amino acid. Because these files can be very large, many times they are compressed using the program `gzip` to save time downloading and disk space. Gzipped files are denoted with `.gz`. 

**Question 8:** What command would you use to count the number of proteins in this file? How many proteins are there?


### Fastq files

Fastq files are the most common modern format for storing sequencing reads. FASTA and FASTQ are rather similar, but FASTQ is almost always used for storing sequencing reads and contains associated quality values foe each nucleotide. 
