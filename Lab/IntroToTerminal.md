# Intro to terminal and Linux

One of the most fundamental parts of bioinformatics is a basic familiarity with the command line. Many things are Linux and bash based, so these are good skills to develop. 

### Basic Linux rules

* Everything is case sensitive. Gen711 is not the same as gen711.

*Spaces in file names should be avoided. Spaces are a special character (represented as `\s`) and thus make things wacky. 

*Your `$PATH` is the collection of locations where the computer looks for executables (programs).

*Folders and Files are all you have. If you want to access one of these, you need to tell the computer EXACTLY where it is. 
`/home/stuckert/gen711/exam1_key.txt` will work (assuming you’ve spelled things correctly, and that the file really exists in that location), but `exam1_key.txt` may not.

*Related to the above, any file/location can be specified relative to your current working directory (folder). So for example if I'm in the `gen711` folder from the above example, I can point to the exam 1 key with `exam1_key.txt`. If I am in the `stuckert/` directory, I can point to the same file with `gen711/exam1_key.txt`. Alternatively, I can use the *absolute path*, which specifies the full path to a file. In this case, I can use `/home/stuckert/gen711/exam1_key.txt` to point to this exam key *no matter what directory I am currently in*.

*In this document, as in scripts generally, lines that begin with a `#` are comments and are not run.

### Introduction to Ron, the UNH training High Performance Cluster for this course.

In order to access this cluster, we have to open a terminal window. 

##### Mac users
* Unix shell: Terminal, which is pre-installed on MacOSX. You can find it through the Finder by going to Go-> Utilities-> Terminal.

##### Windows users
* PuTTY: A terminal which you can install from [the putty website](https://www.putty.org/)
* MobaXterm: A terminal which you can install from [the MobaXterm website](https://mobaxterm.mobatek.net/)

Once you have these installed, we can access your Ron account and get you rolling! In order to access the cluster, you have to point your computer to the location you want to login to. The address for Ron is `ron.sr.unh.edu`, and to login you specify `your_username@ron.sr.unh.edu` Your Ron username should be your UNH username. You use the `ssh` command to login. So, to access your account you would use:

```bash
ssh your_username@ron.sr.unh.edu
```

Your terminal will then prompt you to provide your login information. If you are using PuTTY, you can save the username and IP address and load the connection without the command line.

### Basic commands
*`pwd` print working directory
* `ls` list directory contents

* `nano` open up a text editor

* `mkdir` make a directory (folder)

* `cd` Change directories

* `rm` delete a file

* `mv` move a file

* `cp` copy a file

* `man` gives information about a program. This is very useful.

### Practice some basic commands


