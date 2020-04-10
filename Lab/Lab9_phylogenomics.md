## Using phylogenetic tools to create a gene tree

****


For this lab, we will need a way of viewing a phylogenetic tree. While there are some online options for doing this, none of them had the features I was looking for, so we are falling back on a program I use to look at phylogenies all the time. It is called FigTree and you can download it here: https://github.com/rambaut/figtree/releases.  

I just downloaded the latest version because I hadn't updated mine in a while, and it is very simple to do. Just click on the fist link you see on that page (FigTree.v1.4.4.dmg). If this doesn't work and you can't open it, try the one directly beneath that. On my computer, a window pops up that wants to know what to do with the file; I have "open" highlighted and click "OK". When it is finished downloading, a little window pops up with the figtree icon and a number of other files. You can read the README.txt file for more information, but you should also be able to click on the icon that says "FigTree v1.4.4" and open the program. On my computer, it gives me a warning message because I downloaded it from the interwebs and it wants to make sure it's not going to infect my computer (relatable) or anything. And then you should be all set!  

If this does not work for you, or for whatever reason you are unable to do this, please let me know and we'll find an alternative. On to the lab!  


### First, we need data

Go ahead and log onto your Ron account.  

Let's make a new directory for this lab so that your files won't be in with everything else.  

    mkdir phylo_lab  
    cd phylo_lab

Since the genomes and protein sets they have on Ensembl are already annotated with the sequence identities, they are a great place to start for a tree like this. We are going to be working with rodents again, mostly because they are easy to find, pretty diverse, and ensembl has a LOT of them.  

We will need quite a few of them for this tree, and you will have to do some commands to each dataset that we download, so strap in.  

First, download the datasets from these links just like we've done before:
- American beaver - ftp://ftp.ensembl.org/pub/release-99/fasta/castor_canadensis/pep/Castor_canadensis.C.can_genome_v1.0.pep.all.fa.gz  
- Arctic ground squirrel - ftp://ftp.ensembl.org/pub/release-99/fasta/urocitellus_parryii/pep/Urocitellus_parryii.ASM342692v1.pep.all.fa.gz  
- Degu - ftp://ftp.ensembl.org/pub/release-99/fasta/octodon_degus/pep/Octodon_degus.OctDeg1.0.pep.all.fa.gz  
- Guinea pig - ftp://ftp.ensembl.org/pub/release-99/fasta/cavia_porcellus/pep/Cavia_porcellus.Cavpor3.0.pep.all.fa.gz  
- Lesser Egyptian jerboa - ftp://ftp.ensembl.org/pub/release-99/fasta/jaculus_jaculus/pep/Jaculus_jaculus.JacJac1.0.pep.all.fa.gz  
- Long-tailed chinchilla - ftp://ftp.ensembl.org/pub/release-99/fasta/chinchilla_lanigera/pep/Chinchilla_lanigera.ChiLan1.0.pep.all.fa.gz  
- Mouse - ftp://ftp.ensembl.org/pub/release-99/fasta/mus_musculus/pep/Mus_musculus.GRCm38.pep.all.fa.gz  
- Naked mole-rat (female) - ftp://ftp.ensembl.org/pub/release-99/fasta/heterocephalus_glaber_female/pep/Heterocephalus_glaber_female.HetGla_female_1.0.pep.all.fa.gz  
- Northern American deer mouse - ftp://ftp.ensembl.org/pub/release-99/fasta/peromyscus_maniculatus_bairdii/pep/Peromyscus_maniculatus_bairdii.HU_Pman_2.1.pep.all.fa.gz  
- Prairie vole - ftp://ftp.ensembl.org/pub/release-99/fasta/microtus_ochrogaster/pep/Microtus_ochrogaster.MicOch1.0.pep.all.fa.gz  
- Rabbit - ftp://ftp.ensembl.org/pub/release-99/fasta/oryctolagus_cuniculus/pep/Oryctolagus_cuniculus.OryCun2.0.pep.all.fa.gz  
- Rat - ftp://ftp.ensembl.org/pub/release-99/fasta/rattus_norvegicus/pep/Rattus_norvegicus.Rnor_6.0.pep.all.fa.gz  
- Squirrel - ftp://ftp.ensembl.org/pub/release-99/fasta/ictidomys_tridecemlineatus/pep/Ictidomys_tridecemlineatus.SpeTri2.0.pep.all.fa.gz  
- Upper Galilee mountains blind mole rat - ftp://ftp.ensembl.org/pub/release-99/fasta/nannospalax_galili/pep/Nannospalax_galili.S.galili_v1.0.pep.all.fa.gz

**Question 1:** One of these organisms is the one we are going to use as the outgroup for this particular phylogenetic tree. It is the only one that is not a rodent. Which is it?   

Next, go ahead and unzip all these files using the `gzip -d` command.  

These files (annoyingly) have sequences that are wrapped - meaning they are not all on one line in the file. We will have to unwrap them before we move on. Below is the code you will need to do the first file; make sure you do this to all of them.  

    awk '{if(NR==1) {print $0} else {if($0 ~ /^>/) {print "\n"$0} else {printf $0}}}' Castor_canadensis.C.can_genome_v1.0.pep.all.fa > beaver_unwrap.fa  



### Pulling out the sequences we want  

Now that we have the data downloaded and its formatting changed, we are ready to extract the sequences we want for this tree. Today we're going to be making a gene tree for the Sonic Hedgehog gene. Yes, this is a real thing. You can read about it if you like (https://ghr.nlm.nih.gov/gene/SHH#synonyms), but it is important in development so we know all of these rodents will have at least one version.  

**Question 2:** Use `grep` to search for this gene ("sonic" works well as a search term) in each rodent file, and put the corresponding header(s) and sequence(s) into a new file **that is specific to that particular animal**. Paste a copy of one of these commands for this question. Make sure each rodent has its ***own*** file with its sonic hedgehog gene sequences inside.  

Take a look at one of these files. There is a lot of information in each of the headers, but most of it is no longer very useful to us. What we *do* want in the headers is a really clear species name, so that we'll be able to tell which sequence is which on the tree later. The command below will change the header of a fasta file; do this to all of the sonic hedgehog files. Note: this works for mine because this is what my file was called - make sure you change the input and output file names to match what you want yours to be called.  

    awk '/^>/{print ">beaver_" ++i; next}{print}' beaver_sonic.fa > header_beaver_sonic.fa  


**Question 3:** When you have all of your headers edited appropriately, combine all of those files together. The file you end up with should be a single fasta file with all of the sequences for the sonic hedgehog gene, and all of the headers simple and informative. Paste in the code you used to do this.  


### Aligning the sequences and making the tree  

We'll use mafft again with the default settings for the multiple sequence alignment. Put your own input and output file names in the appropriate places.  

    mafft --auto input.fa > output.fa  


And finally, we will use a program called iqtree to make the tree. Iqtree has a **LOT** of options, but we will try to keep things relatively simple for today. Your output file from the last command will be your input file for this one.  

- `-s` is the name of the input file  
- `-m` is the model we will use - this one is just the initials of the people who created it  
- `-bb` tells iqtree that we want to do some rapid bootstrapping of our tree, and how many subsets to take  
- `-pre` is the prefix of all the output files - iqtree will produce a number of files, and this will be the beginning of all of them   

    `iqtree -s input -m LG -bb 1000 -pre output`  


### Viewing the tree  

One of the files iqtree produces has "contree" at the end of it. Cat this file onto the screen, and copy everything you see including all the strange looking punctuation.    

Paste into FigTree. Literally just command/control v into the open white space and your tree should appear.  

The first thing I like to do is make the tip labels bigger. Over on the side there is a tab called "Tip Labels" and if you click the down arrow on the left side of it, you will see the controls to mess with font size and other things.  

As with all phylogenetic trees, we can arrange this tree in a lot of different ways without actually changing the information displayed. Click on the branch leading to the outgroup you identified. It should highlight, and then go up to the buttons at the top and click the third one from the left that says "Reroot". Now we have a rooted tree!  

Check out the relationships. Some of the animals had two sonic hedgehog sequences, and some only had one. Do the relationships that you see here make sense to you? Check out this wikipedia tree (it's actually pretty good) and the tree down in the classifications below it https://en.wikipedia.org/wiki/Rodent#Standard_classification to see what the relationships within Rodentia look like. **Question 4:** Why might your tree look different from the other examples of rodent trees? Think about what you used to make this tree, and why that might or might not be a good set of sequences to reconstruct this phylogeny.  

Next, click on the box next to "Node Labels". If you click on the down arrow, it give you the option to display different measures, and we want the one that says "label". These numbers are just whatever numbers were on our tree (FigTree doesn't know any better), and in this case they are bootstrap values. Remember that the way the tree gets scored (in this case) is a random resampling of the data. So iqtree will randomly pick a subset of the amino acids and make a tree with these new sequences. Then it compares the topology of the subset tree with the original one, and all the branches that are the same get a 1, and all the ones that are different get a 0. The bootstrap values you see on the tree now are percentages of how often that particular node scored a 1 (out of 1000 times), so you can almost think of it like a grade. Node scored a 95? Excellent. Node scored a 35? We should probably not base too many hypotheses on this one. **Question 5:** Are these numbers what you expected? Is your tree well-supported? Explain what you did expect and why they might be lower or higher than your expectation.  

**Question 6:** Just like last lab, we want to make sure we're cleaning up the things that we've downloaded onto Ron, so make sure you delete all of the genome files you downloaded from Ensembl, and the versions that we "unwrapped" with the first awk command. The files with just the sonic genes in there are smaller and not as big of a deal, but feel free to clean those out too if you're feeling inclined. Paste in a screenshot of the contents of your directory to show me that the genome files are gone.
