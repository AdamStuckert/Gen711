# Intro to terminal and Linux

One of the most fundamental parts of bioinformatics is a basic familiarity with the command line. Many things are Linux and bash based, so these are good skills to develop. 

### Basic Linux rules

*Everything is case sensitive. Gen711 is not the same as gen711.
*Spaces in file names should be avoided. Spaces are a special character (represented as `\s`) and thus make things wacky. 
*Your `$PATH` is the collection of locations where the computer looks for executables (programs).
*Folders and Files are all you have. If you want to access one of these, you need to tell the computer EXACTLY where it is. `/home/stuckert/gen711/exam1_key.txt` will work (assuming you’ve spelled things correctly, and that the file really exists in that location), but `exam1_key.txt` may not.
*Related to the above, any file/location can be specified relative to your current working directory (folder). So for example if I'm in the `gen711` folder from the above example, I can point to the exam 1 key with `exam1_key.txt`. If I am in the `stuckert/` directory, I can point to the same file with `gen711/exam1_key.txt`. Alternatively, I can use the *absolute path*, which specifies the full path to a file. In this case, I can use `/home/stuckert/gen711/exam1_key.txt` to point to this exam key *no matter what directory I am currently in*.
*In this document, as in scripts generally, lines that begin with a `#` are comments and are not run.

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
