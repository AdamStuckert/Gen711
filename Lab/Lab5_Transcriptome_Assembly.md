### Transcriptome assembly

Today we will be doing a *de novo* transcriptome assembly. Similar to last week, we are going to compare a couple of different metrics!

Background information: You suddenly found yourself as a newt (you got better), and you want to assemble your transcriptome! This seems like a totally fine thing to do, and there isn't any sort of ethical issues with assembling your own genomic data. 

Usually, you will get quite a bit of sequence data in order to do any transcriptomic type project (often hundreds of millions of reads). For the purposes of this lab, I've downloaded data from this paper by Glass et al. (https://www.biorxiv.org/content/10.1101/653238v1.abstract) and randomly subsampled down to a dataset that we can work with for the confines of our lab time. To do this I used the `seqtk sample` command. `seqtk` is an extraordinarily useful series of scripts for those of us working with genomic data. I can't recommend it enough.


For lab today we will be taking two different approaches to transcriptome assembly. In both we will be removing sequencing adaptors, error correcting reads, and then assembling a transcriptome. One will use a pretty common transcriptome assembler, Trinity (website: https://github.com/trinityrnaseq/trinityrnaseq/wiki). For the other assembly, we will use the Oyster River Protocol (website: https://oyster-river-protocol.readthedocs.io/en/latest/) that we read about for class today. This lab has a number of steps, and uses quite a few software packages. Because of this, I've chosen to provide a jetstream image with everything pre-installed for the first assembly. This image is called `GEN711_Transcriptome_Assembly`. When you login to jetstream to start an instance, make sure you search for "GEN711" and launch the correct image. Please use `m1.large` for this lab. Students with last names A-M please launch from `Jetstream - Indiana University`, students N-Z please launch `Jetstream - TACC`. This is a trial to see if splitting it up will decrease launch times for everyone.


First, launch the instance. The next thing you will need to do is add conda to your path. Whenever you install software for yourself you should make sure it is in your path. To refresh your memory, your path is where the computer looks for installed executables (programs).

```bash
echo ". /usr/anaconda/install/etc/profile.d/conda.sh" >> ~/.bashrc
source ~/.bashrc
```

Next, activate the conda environment I created for today's lab. You did this last week, but you produced your own. List all the available conda environments with `conda info --envs`. You'll see two environments, one is base and the other is the one I created. Load the environment that is listed with `conda info --envs`.

```bash
conda activate ENVIRONMENT # replace ENVRIONMENT with the name
```


Now we will download our newt data!

```bash
mkdir newt
cd newt
wget https://raw.githubusercontent.com/AdamStuckert/Gen711/master/Lab/Files/Taricha_granulosa_subsampled.1.fq
wget https://raw.githubusercontent.com/AdamStuckert/Gen711/master/Lab/Files/Taricha_granulosa_subsampled.2.fq
```

Verify that you have both datasets and that they are approximately the same size with `ls -lht`. Next, I'd like you to count the total number of reads for both the forward and reverse datasets. 

**Question 1:** how many reads are in each dataset, and what command did you use to count them? Hint: you can use the format of fastq files to your advantage here. Hint #2: We have not talked about `regular expression` yet, but it is a very powerful way to manipulate data for your purposes. The `^` symbol refers to the start of a line, so you can combine that with some search term to find the answer you want (specify it in quotes like this `"^"` with whatever search string you are using inside those same quotes.

A general note about running things on any HPC: You need to keep in mind the resources you are requesting for a particular bioinformatic task. Not only do you want to be careful with our allocation for the class (if we run out, we are in trouble. terminate your instances when you are done!), but also because you want to be able to run tasks on a cluster. If you ask for too many resources, you might end up in a queue and it will take too long--or you may end up using more than you are alloted and you will error out. The latter is likely to be the case here--if you request too much memory, you can get into this situation. Just something to be cognizant of, particularly when you begin your projects!

Back to the regularly scheduled lab. First we will run Trimmomatic to trim off adaptor sequences.

```bash
trimmomatic PE -threads 9 -baseout Taricha_granulosa_subsampled.TRIM.fastq Taricha_granulosa_subsampled.1.fq Taricha_granulosa_subsampled.2.fq LEADING:3 TRAILING:3 ILLUMINACLIP:${MAKEDIR}/barcodes/barcodes.fa:2:30:10:8:TRUE MINLEN:25
```

Finally, we will run Trinity to assemble our transcriptome! Trinity runs in 3 parts (inchworm, chrysalis, butterfly), and will take a little bit of time to run.

```bash
Trinity --SS_lib_type RF --no_version_check --bypass_java_version_check --no_normalize_reads --seqType fq --output trinity/ --max_memory 20G --left Taricha_granulosa_subsampled.TRIM_1P.fastq  --right Taricha_granulosa_subsampled.TRIM_2P.fastq  --CPU 9 --inchworm_cpu 9 --full_cleanup
```

Good work! You've now assembled your first transcriptome! Lets take a look at the assembly quality using the two programs we talked about in class: Transrate and BUSCO. First we will use Transrate.  **Question 2:** What does Transrate tell us about our transcriptome?

```bash
transrate -o transrate -a trinity.Trinity.fasta --left Taricha_granulosa_subsampled.TRIM_1P.fastq  --right Taricha_granulosa_subsampled.TRIM_2P.fastq -t 9
```

**Question 3:** What is the assembly score and the optimal assembly score for this transcriptome? You might have to kick around in the output files to find the answer you want! 
 
Now, we will look at BUSCO scores. **Question 4:** Why do we use BUSCO and what does it tell us about our transcriptome?


First we need to download a BUSCO database and decompress it.

```bash
wget http://busco.ezlab.org/v2/datasets/metazoa_odb9.tar.gz
tar -xzvf metazoa_odb9.tar.gz
```

Next we run BUSCO, pointing it to the metazoa lineage dataset that we just downloaded and decompressed.

```bash
run_BUSCO.py --lineage metazoa_odb9 -i trinity.Trinity.fasta -m transcriptome --cpu 9 -o busco_trinity 
```

When we run BUSCO we point it to a lineage dataset. This lineage dataset should generally be as taxa specific as possible. There are some exceptions to this, such as if you are doing work that crosses the BUSCO taxa groups (e.g., if you are comparing transcriptomes across all Metazoa it would be inappropriate to run BUSCO using the tetrapoda lineage dataset for a dog and the aves lineage dataset for a chicken). To reduce the overall time for lab, we will use the Metazoa lineage dataset, which has many fewer shared single copy genes than the more specific Tetrapoda (which we would otherwise use with a newt).

You've now run BUSCO on your assembled transcriptome. **Question 5:** What is the proportion of each of the following in your assembly?
	1. Complete, single copy genes
	2. Complete, duplicated genes
	3. Fragmented genes
	4. Missing genes


Next, we will move on to a newer approach to transcriptome assembly. This is the Oyster River Protocol. Oyster River may sound familiar, that is because it is named for the Oyster River right by us, and this pipeline was developed by Dr. Matt MacManes here at UNH. We read about it for class today. Hopefully you remember something about it.

This pipeline is reliant on a whole bunch of independent software pieces. Installation could take forever if you manually did each piece, or even if you used conda. For this particular task we will use Docker. Docker is like Conda in that you can have package controlled environments. However, in Docker you download a completely compiled environment, and therefore there isn't any sunk time compiling (or the associated weeping and gnashing of teeth when things don't compile correctly/have dependency issues). The Docker equivalent of conda's environments are called images. The other thing about docker is that in order to run things we have to start the image and either do all the work inside the newly created container and copy the data outside it, or "mount" part of your current drive to be used by Docker. For our purposes we will just start a new Docker container and run everything within it.


**IMPORTANT NOTE: For this portion of lab we will start another instance! Please save all the data you need from the first half of lab (BUSCO + transrate score information) and delete that instance.** After doing this, start a new instance. Use a `m1.large` size version of the `Ubuntu 18.04 Devel and Docker` image.

There are some weird, jetstream specific Docker issues. After logging in do this:

```bash
sudo usermod -a -G docker USERNAME
```

Now you have to **log out and log back in to get this to work**. You can just type `exit` and then log back in via your `ssh -i` command again. Now we can use docker. Install the image.

```bash
docker pull macmaneslab/orp:2.2.6
```

Next you have to start up a container of the image. 

```bash
sudo docker run -it macmaneslab/orp:2.2.6 bash
```

Now you will need to download the data again, since we didn't mount a directory. In reality, I would probably mount it as a general rule. But there is something a bit funky about how jetstream works with docker and user permissions, so this is a work around. You can always copy things outside of the docker container (we may do this later this semester, or may not).

```bash
mkdir newt
cd newt
wget https://raw.githubusercontent.com/AdamStuckert/Gen711/master/Lab/Files/Taricha_granulosa_subsampled.1.fq
wget https://raw.githubusercontent.com/AdamStuckert/Gen711/master/Lab/Files/Taricha_granulosa_subsampled.2.fq
```

Check everything exists!

```
ls -lht
```

Activate a conda environment

```
conda activate orp
```

Run the protocol...

```
$HOME/Oyster_River_Protocol/oyster.mk \
STRAND=RF \
MEM=15 \
CPU=9 \   
READ1=Taricha_granulosa_subsampled.1.fq \
READ2=Taricha_granulosa_subsampled.2.fq \
RUNOUT=newt
```

Ok, so jetstream is interpreting these all as separate lines. So to make your life easier, you can just paste this into a text editor (NOT Word!!) and then make it a single line. Paste that in. Or just type it all! It will give you the full life experience :)

**Question 6:** how do the BUSCO scores compare between the assemblies?

**Question 7:** how do the transrate scores compare between the assemblies?

**Question 8:** Given this information, which assembly do you think is better and why?

**Question 9:** What is different about these two approaches to transcriptome assembly? You may need to look at the ORP code to figure this out!
