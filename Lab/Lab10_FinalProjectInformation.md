### Lab 10--Intro to workload managers

For this lab we will be learning the basics of two things:

1. The additional cluster you all have access to for your final projects.
2. Workload managers.

Before I go through those things very briefly, I will just give some general tips. 

## Project tips for Jetstream

Many (all?) of you may be able to get by with just using Jetstream without having to deal with accessing the new cluster or learning a workload manager (Slurm in this case). So, here are some tips for you.

1. Use `tmux` for your project. `tmux` allows you to have multiple smaller panes in one window (which is cool), but more importantly for us allows you to run things without worrying about disconnect signals interrupting your work. Often what happens is if you run something and lose internet connection or shut down your terminal it will send a signal interruption message and the running task will die. `tmux` and programs like it allow you to run things without worrying about this. You can attach to these tmux windows and run things without worrying that your signal will get interrupted. The other benefit is that we can suspend your instances, which means that we are not being charged as much for your instance.

You can conda install `tmux`. Install conda like you have before and then install tmux. Information here: https://anaconda.org/conda-forge/tmux. Once you have installed tmux, create a new window:

```bash
tmux new -s final # you can call it whatever you want instead of "final"
```

**Question 1:** Send me a screenshot of you in your tmux window.

You can now exit and enter your tmux window as you want to! To detach from your window type `ctrl + b, then d` (ctrl and b simultaneously, release them, then press d).

**Question 2:**  Send a screenshot of your terminal after you detached.

To reattach to your tmux window you can type `tmux at -t final`. If you named it something other than `final`, type that instead. Remember, if you are using tmux, and you close/restart your computer, you will be disconnected from your instance. This is ok. Just reconnect to your instance, then re-attach to your existing tmux window, and you will back to where you left off.

2. Suspend your instance when you are not using it. This way we do not pay for the machine, but your work is saved. When you are in the webpage for your instance you can click suspend. Do this for your current instance. **Question 3:** Screenshot of your suspended instance.

3. If you have a lot of data/reads, please subsample your dataset down for your own ease/to make your project feasible in the immediate future. You can do this with the program `seqtk`, which is also available via conda. Here is how an example of seqtk use to subsample down to 20M reads. The `-s` flag is a seed. Make it whatever you want, but the same between forward and reverse reads from the same individual.

```bash
seqtk sample -s2343 SRRNUMBER_1.fastq.gz 20000000 > reads.1.fq
seqtk sample -s2343 SRRNUMBER_2.fastq.gz 20000000 > reads.2.fq
```

4. Ask us questions if you have them! Send both Jennifer and Adam emails with your screenshots of issues plus a full description. Attend our office hours. Ask for extra meetings. 

5. **Don't wait until the last minute, things take a while to run and longer than you expect to troubleshoot!!!**

6. Download your results from your instance BEFORE you delete it! You will need to do that via another terminal window/tab (ie, you cannot be logged in to Jetstream when you attempt this). Here is how to do it:

```bash
rsync -av -e "ssh -i $HOME/jetkey" USERNAME@INSTANCE_IP_ADDRESS:/ABSOLUTE_PATH_TO_FILES/FILES ~/Desktop/ 
#### Notes:
# change "$HOME/jetkey" to wherever your jetkey lives
# change "USERNAME" to your username
# change "INSTANCE_IP_ADDRESS" to whatever your instance IP address is (what you use to connect to the instance)
# change "/ABSOLUTE_PATH_TO_FILES/FILES" the the absolute path to your file + file name(s) of the files you want to transfer
# change ~/Desktop to wherever you want it on your own machine.
```

`rsync` is a bit challenging to get a hang of but is definitely worth it.

## Bridges

**Note:** We did say you would all use Bridges for your final projects. However, they have devoted the vast majority of their resources to fighting the COVID-19 pandemic. They currently are asking for all work to be submitted with a 12 hour maximum--which would be difficult for much of your work! They also explicitly say that if you ask for longer and you aren't doing coronavirus work you are unlikely to be able to run your code. So, probably not worth it to try to use Bridges.

You may need to use Bridges for your project for two main reasons. The first is that Ron does not have the computing resources or hard drive space for all of you to be doing larger computational jobs. The second is that while you have the ability to do some of these things on Jetstream these are ephemeral Virtual Machines that get wiped every time you delete one. I don't anticipate that you will need Bridges. If you do, this is the information for it: https://portal.xsede.org/psc-bridges. You can log in via `ssh` like you have before:

```bash
ssh USERNAME@bridges.psc.xsede.org -p 2222
```

If you use putty, you can specify port "22" in the login. Note if you think you need Bridges please email Jennifer and Adam and we will help determine if you do and help you get on if that is the case.

## Slurm

Slurm. Slurm. Slurm. What a ridiculous word. Anyway. Slurm and other workload managers are a solution to managing many users who all may want to be using the system's resources at the same time. Clusters that don't spin up Virtual Machines (like Jetstream does) that don't use workload managers are **PURE CHAOS** and should be better managed. 

In slurm and similar programs you specify the resources that you want for a job at the top of a submission script. These are generally somewhat cluster-specific. Often you will specify how many threads, memory, and time you want. Here is an example of one of mine on Bridges:

```
#!/bin/bash
#SBATCH -N 1
#SBATCH -p LM
#SBATCH --mem 1000GB
#SBATCH -t 330:00:00
# echo commands to stdout
set -x

# code/scripts follow!
```

Anyway, all likely moot because of coronavirus.
