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

Next, activate the conda environment I created for today's lab. You did this last week, but you produced your own. List all the available conda environments with `conda info --envs`. You'll see two environments, one is base and the other is the one I created. Load the environment that is listed with 

```bash
conda activate ENVIRONMENT # replace ENVRIONMENT with the name
```
