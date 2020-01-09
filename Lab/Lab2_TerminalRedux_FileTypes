## Intro to terminal continued

For today's lab we will continue our introduction to basic Unix and working in the terminal. We will also begin to work with common file types used in genomics and bioinformatics. You will see many of these file types as you use them throughout your bioinformatical career, whether that lasts for the length of this class or a lifetime.

*Programming note:* _ba dum tss!_

Much of today's material is heavily borrowed from Dr. Chris Balakrishnan's bioinformatics workshop.

First we will begin by learning to download some data from online. There are a variety of ways to do this, but for today we will use `curl`. This is a very useful and important aspect to bioinformatics. I have a compressed folder of files stored on the course's GitHub page. Download this:

```bash
curl LINK
```

Next, view your current directory. Think back to last week to do this. Once you have done this, you should be able to see that you have downloaded a new file! You did this without a user interface or clicking anything! It should be called `Lab2.zip`. Because this is a compressed folder, if you view this with `less Lab2.zip` you should be able to see the contents of the folder. Before accessing any of them, you will have to uncompress this folder first:

```bash
unzip Lab2.zip
```

You can view the contents of your directory--it should now contain all of the same files that you saw when you used `less` to view the zipped directory. We will use these files to start with a bit of a review from last week. Let’s take a peek at the `darwin_chapter.txt` file using the ‘more’ command.

```bash
more darwin_chapter.txt
```

This is the last chapter from Charles Darwin’s last chapter of The Origin of Species’. Press space bar to continue scrolling through the file, type ‘q’ to quit. Now let us say that you want to only look at the first few lines of the file, just to verify it is what you think it is. You can do this by using the ‘head’ command. *Question 1: how many lines does head show?*

```bash
head darwin_chapter.txt
```

To specify the number of lines viewed you can use the -n flag. Show the first 100 lines of the file.

```bash
head -n 100 darwin_chapter.txt 
```

To view the last few lines of a file, use the tail command.

```bash
tail darwin_chapter.txt
```

*Question 2:* How many lines are in this text file?

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

Now, you may be interested in counting exactly how many times he says this. Luckily, we have a tool called *piping*, which allows us to link commands together. This is one of the most useful tools in your toolbox. What piping does is say take the output from the first command and use this to run the second command. Since grep outputs a single line from the file each time it finds the requested string, we can then pipe this in to the `wc` command to count the number of times he says grandeur. *Question 3: *How many times does Darwin use the word "theory"?

```bash
grep "theory" darwin_chapter.txt | wc -l
```

*Question 4:* Why might counting the number of times Darwin used theory in this way be problematic and imprecise?

