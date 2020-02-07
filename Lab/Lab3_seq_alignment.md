### Setting up your jetstream project and your key

Navigate to https://use.jetstream-cloud.org/

1. Click the login button at the top right of the screen.
2. Log in using the xsede account you set up.
3. Click on "Projects" (second tab).
4. Click "Create New Project".
5. Give it an informative name and description, then click "Create".
6. **In your terminal** type `ssh-keygen -t rsa -f $HOME/jetkey`.
7. It will ask you for a password - make sure you make it something you will remember.
8. It will generate a random picture for you - this is normal - and then type `more $HOME/jetkey.pub | cut -d " " -f1-2 | pbcopy`. The last step of this command copies the newly made key, so we can paste it later.
9. **Back on the jetstream page** click on your username in the top right corner and select "Settings".
10. Scroll down and click "Show More".
11. Click the plus sign at the bottom and a window should pop up titled "Add a public SSH key".
12. Name the key "jetkey" and paste (that key we copied in step 8) into the public key area. Click "Confirm".

**Steps 1-12 you *should* only have to do once, provided you are working on the same computer for each lab**


**The steps below are for launching an instance and connecting to it, which you will do every week.**

### Launching a jetstream instance and connecting to it via the terminal

1. Go back to the "Projects" tab and select the project you made earlier.
2. Click the pink "NEW" button on the left and select "Instance".
3. Search for (or scroll down to find) the correct type of machine for your instance (Ubuntu 18_04 Devel and Docker) and click it.
4. The third thing down on the right is "Instance Size" - adjust this to whatever size the lab protocol says. For this lab, we want an m1.medium instance.
5. Click "Launch Instance"
6. The instance will take a second to activate and launch fully. Sometimes it takes longer than other times. Be patient.
7. Copy the IP address of the instance once it is available.
8. **Back in the terminal** type `ssh -i $HOME/jetkey username@xxx.xxx.xxx.xxx` to connect to your instance. Replace "username" with your jetstream username, and "xxx.xxx.xxx.xxx" with your IP Address that you just copied.
9. When it asks you if you want to continue connecting, type "yes" and hit enter. 
10. Type in your password when prompted.
11. Should be connected!



Now we can get things set up.
Your instance is a bit of a blank slate, so we'll need to install the programs we'll use in lab.

The first few commands we will use start with "sudo" - this is telling the computer that we have administrator privileges. They also have `apt-get`, which is just a program used for managing other software packages more easily.

**Question 1:** What happens when you run the first command without the "sudo" at the beginning?

Be careful with `sudo`! Having privileges on a computer means lots of opportunities to mess things up or delete things that are important. It is pretty rare that you will have these privileges on a computer, or you could get into trouble for trying them, so don't just go around sudoing things. Best to use it only on these instances where we can always terminate the instance and start over.

First we need to check the server for updates to existing software
> sudo apt-get update

Then, we install any required updates
> sudo apt-get -y upgrade

Now, we need to install some basic software that will allow us to do a lot more. Same structure as previous commands, but `install` instead of `update` or `upgrade`. See how this works?
Everything after the `install` part is a list of things we are installing.
> sudo apt-get -y install ruby build-essential python python-pip gdebi-core r-base git

Next, we'll install Conda. Conda also manages software packages, but specifically scientific ones - we will use this all the time.
> mkdir anaconda
> cd anaconda
> curl -LO https://repo.anaconda.com/archive/Anaconda3-5.1.0-Linux-x86_64.sh
> bash Anaconda3-5.1.0-Linux-x86_64.sh -b -p $HOME/anaconda/install/
> echo ". $HOME/anaconda/install/etc/profile.d/conda.sh" >> ~/.bashrc
> source ~/.bashrc
> conda update -n base conda

Now we'll make a "conda environment" that will hold all of our installations, activate it, and install the programs we'll need for today's lab. Recognize any of them?
> conda create -y --name gen711
> conda activate gen711
> conda install -y -c bioconda blast mafft

Return to your home directory. This command will get you there.
> cd

**Question 2:** What other command could you use to return to your home directory from your current location?

### On to the lab itself!

You have just returned from Central America, where you captured (and then, sadly, killed for its RNA...) a never seen before desert rodent. 
You are interested in knowing how it survives, and start by trying to identify the sodium transport genes, in particular a gene called SCN5A.
You've just had the animal's transcriptome sequenced (all of the RNA, after it has been reverse-transcribed into cDNA) and are about to begin the search!


First we need to download the data:
> curl -LO https://s3.amazonaws.com/macmanes_share/transcripts.fasta

Luckily, there are lots of good rodent genomic resources out there, so you can compare what is in the transcripts to some rodents that already have solid genomes to pull out the things you're interested in for the new rodent.
You decide to use a diversity of rodents because you aren't sure which sequences will look most like your mystery desert rodent.

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

We want to make the blast database from a combination of these four rodent files, but blast will only take one file! 
**Question 3:** How could we combine these four reference rodent files into a single file that we could use to build a blast database? Looking for a piece of code here.


#### BLASTing

Now we can build the database blast will use for searching using the command `makeblastdb`.
Notice the flags just like we've been using with `grep` and other programs. In this case, they are pretty informative, but that isn't always true.
`-in` is where you should put the input file - this is the file you made in question 3, above. Make sure you type in whatever you named your final file in that step.
`-out` is what you want your output files to be called. There will be a few of them, so no need to give them a file extension.
`-dbtype` is the type of database, in our case, nucleotide, but you could also do a protein one if that fit your data or question better.
> makeblastdb -in all_ref_rodents.fa -out mus -dbtype nucl

This will take just a little while (mine took 14.1483 seconds) so be patient.

All that is left to do is blast our queries to our database!
> blastn -db rodent -max_target_seqs 1 -query transcripts.fasta -outfmt '6 qseqid qlen length pident gaps evalue stitle' -evalue 1e-10 -num_threads 6 -out blast.out

We have an output file called `blast.out` so **Question 4:** how do we look at it? Copy your code for your answer. There are multiple correct answers here, but some will be a little more manageable than others.
Take a look at the file. The first column is the id number of the transcript from the mystery rodent file, and the 6th column is the e value.
**Question 5:** What is the e value and are higher or lower numbers better?

Oh jeez. The files is super long. How can we find the gene we are actually interested in?
**Question 6:** Find the gene we want (SCN5A) in the output file and copy the command for your answer. *Hint: a `-i` flag might be helpful here.* 

**Question 7:** What is/are the transcript/s from the mystery rodent that had matches to the gene of interest?

Great! Now we can pull out those transcripts from the original transcript file. Keep their headers with them! This is the only way we have of identifying them later.
**Question 8:** Write the code to pull these out and save the results to a file. *Hint: there is a section of the grep man page called "Context line control" that I find fascinating.*

**Question 9:** Pull out the sequences (and their headers) from the file you made the database out of. Add these to the file you made in Question 8 and paste the command(s) for this question.
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

 
 # Remember to terminate your instance when you are finished!
