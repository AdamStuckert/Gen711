## Mapping and expression of RNA seq data

### Mapping

For today's lab we can use the same image we ran last week (`Ubuntu 18.04 Devel and Docker`). I tested this on a `m1.large` instance.

Upgrade/update your image.


```bash
sudo apt-get update
sudo apt-get upgrade
```


Today we only need one a couple of programs, one of which we are going to install in a new and different way! Well, new and different for you, in reality installing this way is the older way. So, let us do our first installation from a binary file.

We are going to install `Salmon`, which we will use to map RNA seq reads.

```bash
wget https://github.com/COMBINE-lab/salmon/releases/download/v1.1.0/salmon-1.1.0_linux_x86_64.tar.gz

# unpack tarball
tar xzvf salmon-1.1.0_linux_x86_64.tar.gz

# verify it exists!
ls salmon-latest_linux_x86_64/bin/
```

You should see `salmon` in the results. Now, export it to your $PATH, which is where the computer searches for executables.

```bash
export PATH=$PATH:$HOME/salmon-latest_linux_x86_64/bin
```

We can make sure this all worked and salmon is functional via two things (both are good practice/useful in my opinion). First, use the program `which` to see where an executable exists.

```bash
which salmon
```

This should show you a path to the executable you just installed. You can do this for any program! Even `which`. Try it with `which which`, that is just fun!

**Question 1:** What happens if there is not an executable? Make up a program name, tell me the command you used to find out, and tell me the output.


The second way to verify the install/addition to path worked is by just calling the executable. For salmon, you would just type `salmon`.


Next up we will do the same for sra toolkit. This is an astonishingly useful tool for downloading data from the SRA (Short Read Archive).

```bash
# download tarball
wget http://ftp-trace.ncbi.nlm.nih.gov/sra/sdk/2.10.3/sratoolkit.2.10.3-ubuntu64.tar.gz
# unpack tarball
tar xzvf sratoolkit.2.10.3-ubuntu64.tar.gz
# export it to your $PATH!
export PATH=$PATH:$HOME/sratoolkit.2.10.3-ubuntu64/bin
```

This program is a bit weird, you have to configure this program. Once we type in the command to configure it, all of a sudden you will have a weird looking terminal with many colors. Do not panic. Or do, I guess. Ultimately it is up to you, but there is no actual reason to panic.

Configure sra toolkit:

```bash
# this is the initial command
vdb-config --interactive

# Now you will have a drastically different terminal

# to save changes, type:
s

# it will tell you there aren't any changes to save, type:
o

# now exit by typing: 
x
```


Now, how would you verify that this worked? One of the executables is called `prefetch`. **Question 2:** what command would you use to verify that the sra toolkit was properly installed by using `prefetch`?


On to the meat and potatoes of lab! Download the data to begin with. We will use the `fasterq-dump` function, which does all sorts of fancy wizardry to just give you the `fastq` files from the archive! This will take a bit of time.

```bash
mkdir data
cd data

# download read data with super useful sra toolkit. This will take a while, but not Forever. If it takes >5 minutes let me know.
fasterq-dump SRR1575395

# download an assembly to map to. This assembly is deposited in DataDryad
wget https://datadryad.org/stash/downloads/file_stream/948 -O brain.fasta
```

**Question 3:** What does the `-O` flag do here?


Now we will map our reads. Like we discussed in class this morning, we have to first index our reference. In this case, we are using a transcriptome (even though this species has a chromosome-level genome assembly we are using the transcriptome for speed in this lab). So first we will index it, and then we will map our samples to this index. In our case, we are only doing 1 sample.

```bash
# index the transcriptome
salmon index --transcripts brain.fasta --index brain_index -k 31

# map the reads to the index
salmon quant -p 10 -i brain_index --seqBias --gcBias -l a \
-1 SRR1575395_1.fastq \
-2 SRR1575395_2.fastq -o salmon_output
```

Now we are going to start working in R to visualize these data! We will need to install some stuff.

```bash
# install R
sudo apt-get install r-base

# install a needed package...
sudo apt-get install gdebi-core

# download r studio
wget https://download2.rstudio.org/server/trusty/amd64/rstudio-server-1.2.5033-amd64.deb

# install r studio
sudo gdebi rstudio-server-1.2.5033-amd64.deb
```

Find out the web address of your server. Paste the web address that comes up on the terminal, in to your browser.

```
echo My RStudio Web server is running at: http://$(hostname):8787/
```

Make a password (make it an easy one!!!). Type this command **exactly as is in your terminal.** After you do this it will prompt you for a password, which you will enter twice. 

```
sudo passwd $(whoami)
```

Now we are in R! Wahoo a new language!

Let's change our working directory. So far we have always used `cd` for this, but in R it is `setwd`.


```R
setwd("data")
```

We can look inside our directory with a similar command. Here do `list.files()` to list all the files (seems kinda intuitive). 

Now we will have to read in our data into R. The data is in the `salmon_output` directory. R is kind of different, because you are setting variables, vectors, etc from Right to Left. This is denoted with a `<-`.

```R
exp <- read.table("salmon_output/quant.sf", header = T)
```

Alright, you've read in data into R for the first time! This literally took me two hours when I taught myself how to do it. Hopefully it took you much less time.....

We can see what is inside our item `exp` by using `View(exp)`. To make things easy, we will subset only the first 100 lines of the dataset. **Question 3:** If you were in a UNIX environment like we have been for lab, how would you create a file called exp2 that was 100 lines from a file called exp?

In R, we can do this with:

```R
exp2 <- head(exp, 100)
```

Now we can make some figures! First, lets make a boxplot of the TPM data. In R, you can specify a particular column of a dataframe (what your data is loaded in) by specifying `DataFrameName$ColumnName`. So to specify the `TPM` column within the boxplot function:

```R
boxplot(exp2$TPM)
```

**Question 4:** Paste in your figure. In the plot viewer you can click zoom then right click and copy image to make it easier.

Now do the same for read length of the transcripts.

There are two big benefits of R in bioinformatics. 1) plotting and 2) statistics (same with Python). You had a prediction that transcript length actually drives gene expression. We can test this in R! A visual of this might suffice (and visualizing your data is always good!). We can make a scatter plot of our data to see the relationship between read length and TPM.

```R
plot(exp2$Length, exp2$TPM)
```

Hmmmm...interesting. Just to make sure, add a trend line!

```R
abline(lm(TPM ~ Length, data = exp2), col = "blue")
```

**Question 5:** Plot your scatter plot + regression line.

**Question 6:** Based on your data, do you think transcript length predicts expression?

**Question 7:** What is a little bit weird about your analyses of expression today? How do you think this would compare to a real analysis of expression data?

## Don't forget to delete your instance!

