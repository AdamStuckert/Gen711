## Intro to terminal continued

For today's lab we will continue our introduction to basic Unix and working in the terminal. We will also begin to work with common file types used in genomics and bioinformatics. You will see many of these file types as you use them throughout your bioinformatical career, whether that lasts for the length of this class or a lifetime.

**Programming note:** _ba dum tss!_

Much of today's material is heavily borrowed from Dr. Chris Balakrishnan's bioinformatics workshop.

First we will begin by learning to download some data from online. There are a variety of ways to do this, but for today we will use `wget`. This is a very useful and important aspect to bioinformatics. I have a compressed folder of files stored on the course's GitHub page. Download this:

```bash
wget https://github.com/AdamStuckert/Gen711/raw/master/Lab/Files/Lab2.zip
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

After viewing this, you may have been surprised to learn how many lines `head` automatically shows! Try this again with a new file:

```bash
head genes.txt
```

This is an interesting example, and highlights something about UNIX files. That is that until there is a return character (called that because you had to physically return the carriage of a typewriter), it is viewed as the same line of text even if it looks like multiple lines on your screen. Return characters are represented with `\n`.

To specify the number of lines viewed you can use the -n flag. Show the first 100 lines of the file.

```bash
head -n 100 genes.txt 
```

To view the last few lines of a file, use the tail command.

```bash
tail darwin_chapter.txt
```

Again, it might be easier to view what tail accomplishes with the `genes.txt` file.

When you are working with data, you will want to sprinkle in many, many little sanity checks. This is especially true if you are trying to manipulate data or files in some fashion. One way I do this frequently is by counting lines of a file (or counting lines in the output of a chunk of code). You can do this with the `wc` command. `wc` is extremely useful! Make sure you look at the various options it has with the `man` program. **Question 2:** How many lines are in this text file?

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

**Question 4:** Why might counting the number of times Darwin used theory in this way be problematic and imprecise? This will take some thinking about things like return characters and the way `wc` works--so make sure you looked at the man page!

### FASTA files

The FASTA format is a common file format that is used for storing DNA, RNA or Protein sequences (generally not raw sequence data). FASTA files begin with a header line starting with ‘>’ that contains text information of the sequence and often an identifier such as the genbank ID (NM_004985.3).  The subsequent lines contain the DNA, RNA or Protein sequence.  Genomic, transcriptomic, and proteomic assemblies will come in this format. FASTA files usually end with the `*.fa` or `*.fasta` extension.

Let’s examine the FASTA file for the KRAS gene
```bash
more kras.fa # remember you can exit more, less, and similar programs with the "q" button
```

**Question 5:** how many lines are in this fasta file?

Remember the first day of class when we counted nucleotides? It was awful. We can use commands we have already learned in order to count the total number of nucleotides easily! `grep` has a flag (flags are used to specify parameters to a command) that allows us to find the inverse of a search. We can combine that with `wc` to count the total number of characters using the `-c` or count flag.

```bash
grep -v ">" kras.fa | wc -c
```

**Question 6:** how many total characters are in this fasta file?

**Question 7:** how would you count the total number of nucleotides in this fasta file?

**QUestion 8:** given the commands you've learned so far, and your new knowledge about UNIX files, why might you need to be careful counting nucleotides with commands I've given you so far?

### Multi FASTA files

Multi fasta files are the same format as a 'regular' fasta file. However, they contain >1 (usually many more) individual sequences. Each sequence is denoted by the header identifier `>`. The zipped directory you downloaded has a multi fasta file (often referred to as just a fasta file) of protein sequences called `human.pep.fa.gz`. Each character within the sequence is a single amino acid. Because these files can be very large, many times they are compressed using the program `gzip` to save time downloading and disk space. Gzipped files are denoted with `.gz`. 

**Question 9:** What command would you use to count the number of proteins in this file? How many proteins are there?


### FASTQ files

As we learned in class, FASTQ files are the most common modern format for storing sequencing reads. FASTA and FASTQ are rather similar, but FASTQ is almost always used for storing sequencing reads and contains associated quality values for each nucleotide. A single read in a FASTQ file is spread across four lines:

1. `@` followed by the read name
2. Nucleotide sequence
3. `+` followed by information in some cases (largely ignored information)
4. Quality scores for each nucleotide

FASTQ files usually have the extension `.fq` or `.fastq`. We will discuss these more in the lecture section of class.

Take a look at the fastq example in your downloaded zip directory (`example.fq`). **Question 10:** What command would you use to count the number of nucleotides in this file? How many are there?

### Delimited files

Many files that you will see/work with are delimited files, which means that they are basically tables of data with columns separated by a delimiter, and rows separated by a return character. Common delimiters are commas (`,`) and tabs (`\t`). These types of files can be read into typical spreadsheet GUI programs such as Excel (but be careful, they can be hundreds of thousands of rows in length, which will cause your computer to struggle/die). I have an example of a tab-delimited file amongst the files you downloaded. Take a look at it.

```bash
less cancer_genes.bed
```

This file has a number of columns, as you can clearly see. Now, lets say you only care about the gene name, and the chromosome it is on. We can show only those columns in a number of ways, but `cut` will serve our purposes. We can use `cut` to separate the file by a particular character using the `-d` flag, and then get the specific columns we want using the `-f` flag. Importantly, cut will cut at *every instance* of your delimiter, so if your file is irregular you can get weird outputs. Since the program's assumed delimiter is tab (`\t`) and chromosome and gene are in columns 1 and 4 respectively we can use:

```bash
cut -f 1,4 cancer_genes.bed
```

That is a lot of lines of output, and you can imagine seeing this work on a file with a huge number of lines. **Question 11:** Using a *pipe* how would you show only a subset of the output to test that your code worked? 

Testing your code is important, especially if you are manipulating the data/files you have. **Remember anything you do to them is permanent**. 

Since we only care about chromosome number and gene name, lets save this file with only those two columns. You can always redirect the standard output (what gets printed to the screen) using the `>` symbol in your line of code.

```bash
cut -f 1,3 cancer_genes.bed > cancer_genes.bed
```

Now take a look at your new file. **Question 12:** What did you do, and was it what you wanted? How would you change the code to give you what you wanted, and what would you have to do now to get the file you want?

Please remember to fill out the assignment from canvas, save it as a pdf, and submit it via canvas.
