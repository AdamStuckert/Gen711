# Intro to terminal and Unix

One of the most fundamental parts of bioinformatics is a basic familiarity with the command line. Many things are Linux and bash based, so these are good skills to develop. 

### Basic Unix rules

* Everything is case sensitive. Gen711 is not the same as gen711.

* Unlike a GUI interface to view files on your personal computer, once you delete something it is **permanently gone**. You can easily delete an entire hard drive's worth of data by accident in seconds. I call this "pulling a Newhouse." A friend of mine did this to a hard drive in his lab, and there was much swearing.

* Related to the above, there is no undo button. If you write over a file or make a change to it, the deed is done and cannot be undone.

* The two points above are a very good reason why you should 1) be extremely careful when in terminal and 2) maintain good backup copies of all data outside of the environment you are working in! It is unreasonably easy to delete months and thousands of dollars worth of work.

* Spaces in file names should be avoided. Spaces are a special character (represented as `\s`) and thus make things wacky. 

* Your `$PATH` is the collection of locations where the computer looks for executables (programs).

* Folders and Files are all you have. If you want to access one of these, you need to tell the computer EXACTLY where it is. 
`/home/stuckert/gen711/exam1_key.txt` will work (assuming you’ve spelled things correctly, and that the file really exists in that location), but `exam1_key.txt` may not.

* Related to the above, any file/location can be specified relative to your current working directory (folder). So for example if I'm in the `gen711` folder from the above example, I can point to the exam 1 key with `exam1_key.txt`. If I am in the `stuckert/` directory, I can point to the same file with `gen711/exam1_key.txt`. Alternatively, I can use the *absolute path*, which specifies the full path to a file. In this case, I can use `/home/stuckert/gen711/exam1_key.txt` to point to this exam key *no matter what directory I am currently in*.

* In this document, as in scripts generally, lines that begin with a `#` are comments and are not run.

### Introduction to Ron, the UNH training High Performance Cluster for this course.

In order to access this cluster, we have to open a terminal window. 

##### Mac users
* Unix shell: Terminal, which is pre-installed on MacOSX. You can find it through the Finder by going to Go-> Utilities-> Terminal.

##### Windows users
* PuTTY: A terminal which you can install from [the putty website](https://www.putty.org/)
* MobaXterm: A terminal which you can install from [the MobaXterm website](https://mobaxterm.mobatek.net/)

Once you have these installed, we can access your Ron account and get you rolling! 

**BEFORE WE DO THIS, A CRITICAL NOTE:**

If you are on a Mac and working through the terminal, or on a PC an working through MobaXterm, once you open the terminal you are **working in your computer's directories**. This means that you can accidentally delete/corrupt files on your personal computer, **including your operating system.** Please be extremely careful. I am not responsible for any personal sorrow you suffer in this manner.

In order to access the cluster, you have to point your computer to the location you want to login to. The address for Ron is `ron.sr.unh.edu`, and to login you specify `your_username@ron.sr.unh.edu` Your Ron username should be your UNH username. You use the `ssh` command to login. So, to access your account you would use:

```bash
ssh your_username@ron.sr.unh.edu
```

Your terminal will then prompt you to provide your login information. You should have received an email with your login information. Once you login, you will be asked to change your password.  **Remember this new password!** If you are using PuTTY, you can save the username and IP address and load the connection without the command line.
