# Introduction

In this lesson you will connect to an [Amazon EC2](https://aws.amazon.com) cloud computing instance to annotate a bacterial genome, and then mine the annotation to learn interesting things about the biology of the organism. You will work with a partner, but each student will use his/her own remote computer.

# Learning objectives

At the end of today's lesson, students will understand:

*   foundational Unix shell commands (`ls`, `pwd`, `less`, `grep`, others as time permits)
*   directory structure
*   essential shell tricks to keep you from typing more than you have to (tab autocompletion and arrow keys)
*   common bioinformatics file formats and the type of information each contains (`.fasta`, `.faa`, etc.)

At the end of today's lesson, students will be able to:

*   `ssh` to a remote server from the Terminal
*   interact with the Unix shell to extract information from text files
*   explain the computational steps taken to predict and annotate genes in a bacterial genome
*   run [Prokka](http://www.vicbioinformatics.com/software.prokka.shtml) to annotate a bacterial genome
*   execute scripts to answer specific questions about a genome annotation

# Housekeeping items
**Copy-paste:** unless I tell you otherwise, do not copy and paste commands into the Terminal! The point of this lesson is to become familiar with typing shell commands and to learn by repetition. This is one example where strict memorization is essential, and this will only happen if you practice typing EVERY command!

**Stickies:** use green stickies to signal that you are following along with the examples successfully, and use pink stickies to signal any sort of question/confusion/problem.

**[Etherpad:](http://ow.ly/TAwWE)** the Etherpad is an online notepad for sharing notes, questions, and answers to exercise questions. If you have a question, type it into the Etherpad chat (as well as put up your pink sticky) so that your classmates can help.
*Please be careful not to delete others' notes!*

# Unix shell 101
First, we need to log in to the remote Amazon instance. From your Mac, open Applications --> Utilities --> Terminal and type

    ssh student@ec2-??-??-??-??.compute1.amazonaws.com

where `??-??-??-??` is the IP address of the instance you claimed in the [Etherpad](http://ow.ly/TAwWE). Type `yes` to add to the list of known hosts. When prompted, enter your password: `Gen0mic$`

## `pwd` and `ls`: your eyes and ears in the shell

`pwd` stands for print working directory. If you ever take a wrong turn, `pwd` is like GPS. **Note:** For simplicity and to make sure this lesson covers biology and not just shell, the Amazon instances have been configured to show the current working directory before the `$` command prompt.

`ls` lists the contents of the current working directory, and is analogous to graphically viewing the contents of folders on your desktop. The `ls` command commonly takes additional parameters, called **arguments**, which modify its default behavior. Some common ones are:

*   `-l` prints a long list including file attributes (size, ownership, date modified)
*   `-a` prints all files, including hidden files
*   `-t` sorts the list by when the file or directory was last modified
*   `-r` sorts in reverse order
*   `-F` denotes directories with a trailing `/`

In the Unix shell, you can pass multiple arguments together. For example

    ls -lrt

will generate a long list sorted by time, with the most recent file listed last. This command is useful in situations where you want to verify that a file you expect was actually created or modified.

## Tab autocompletion

The Unix shell knows we're all lazy and hate typing, so we can press the Tab key whenever we need to automatically fill in the rest of a long file name. Try it! Type

    D <Tab>
    D <Tab><Tab>

Really, the reason tab autocompletion exists is to reduce or eliminate typing errors because, at least in this respect, computers are smarter than humans.

## `cd`: moving around in the shell

`cd` stands for change directory. Tab autocompletion is your biggest friend with this command! Some examples:

    cd Dbiwa_annotation

changes into the `Dbiwa_annotation` directory.

    cd ..
    
moves up one directory. In Unix, `..` always means up one directory

To get back to your `home` directory (on these instances, this means `/home/student`), you can type any of the following. Since this is such a common task, several redundancies are built in:

    cd
    cd ~
    cd $HOME
    
All of these commands will take you back to the home directory.

## `less` and `grep`: viewing and collecting information from text files

`less` allows you to view and scroll through files without opening them. Let's use `less` to take a peek inside one of the genomes we'll be annotating. Remember to always tab-autocomplete!

    less Desulfuromonas_soudanensis_WTL.fasta

We can navigate through the file with different shortcut keys. The most commonly used are

*   `space bar` - page down
*   `b` - page up
*   `G` - go to end of file (useful for checking file integrity in certain situations)

Like `ls`, `less` can take arguments as well. Two useful ones are:

*   `-S` - do not wrap long lines (useful when a file contains lines that are longer than will fit in your Terminal window)
*   `-N` - print line numbers (useful if you want to slice a file up to a certain line)

`grep` (global regular expression print) looks for lines in a file that contain a match  to a particular search pattern (case sensitive), and prints the entire line to the screen. For example

    grep 'hypothetical' hgcAB.faa

will print any lines in `hgcAB.faa` that contain the search term 'hypothetical'. `grep` also takes some helpful arguments:

*   `-c` print only a count of the number of matches
*   `-v` invert match, i.e. print lines that do NOT contain the search term
*   `-i` ignore case

A common task in bioinformatics is to count the number of sequences in a [FASTA](https://en.wikipedia.org/wiki/FASTA_format) file. In this file format, each sequence begins with a header preceded by a caret `>`. So, a `grep` command to count sequences would be

    grep -c '>' some_file.fasta

Note that a FASTA file can contain either nucleic acid or protein sequence.

# Your first genome annotation
Now we're ready to set up our first annotation. Remember to tab-autocomplete whenever possible to eliminate typing errors and to confirm that the computer is doing what you think it should be. For odd-numbered groups:

    annotate -g Desulfuromonas_soudanensis_WTL.fasta

For even-numbered groups:

    annotate -g Desulfuromonas_biwabikus_DDH964.fasta

## Viewing the annotation

Once the annotation completes (how long do you think it will take?), use your Unix shell skills to move into the annotation directory and familiarize yourself with the different output files. 

    cd Dsoud_annotation

or

    cd Dbiwab_annotation

Use `ls` to list the files and `less` to take a peek at a few of the output files. What kind of information does each file contain? 

# Exercises

**Post your results to the Etherpad as you answer these questions.** Be sure to specify which genome you are working with.

![Koala](koala.jpg)

**1.**   Just like when you collect data at the bench, a good rule of thumb is to never trust your bioinformatics software until you exhaustively reproduce and verify the results. How can we be sure that the annotation we generated isn't full of mistakes? One way we can ensure that annotation errors are not propagated onto new genomes is to use a set of trusted protein sequences from a closely related organism. We will tell the `annotate` command to use the `-p` option and then specify a file of proteins. Type the following (for odd-numbered groups):

    cd
    annotate -g Desulfuromonas_soudanensis_WTL.fasta -p trusted_proteins.faa &

For even-numbered groups:

    cd
    annotate -g Desulfuromonas_biwabikus_DDH964.fasta -p trusted_proteins.faa &

The trailing `&` tells the computer to run the command in the background, so that you can answer the following questions. 

**2.**   Use `grep` to count the number of a) proteins, b) tRNAs, and c) rRNAs and d) ATP synthases in your genome. Do these numbers make sense? Do they agree with the values in the `Dsoud.txt` or `Dbiwa.txt` files?

**3.**   Find the gene locus tag(s) (e.g. DSOUD\_1234, DBIWA\_1234) corresponding to the following:

*   tRNA charged with cysteine
*   sigma factor RpoN
*   the F subunit of NADH dehydrogenase

**4.**   A hallmark of bacteria that respire extracellular metals is an abundance of multiheme *c*-type cytochromes, redox proteins that efficiently move electrons around. Use the following Python scripts to collect information about the putative *c*-type cytochromes in your genome (Tab-autocomplete whenever possible!). For this, you want the `.gbk` (Genbank) file:

    cd
    cd Dsoud_annotation
    get_multiheme_cytochromes Dsoud.gbk
    cytochrome_stats.py Dsoud.gbk

How many cytochromes are there? Use `ls` and `less` to find the locus tag of the largest cytochrome in terms of a) molecular weight and b) number of heme-binding motifs.

**5.**  By now, your second genome annotation from Question 1 should be complete. What arguments can you pass to `ls` to look for a recently generated directory containing the new results?

Let's compare the two annotations:

    cd
    grep -c '>' *annotation/*.faa

In Unix, * means wild card, and the shell will match anything that can autoexpand to fill the wild card.  The above command allows us to count the number of protein sequences in both annotations simultaneously.

Write another `grep` command to count the number of hypothetical proteins in both annotations. Is there a difference? Why or why not? Write your answers to the Etherpad.

# Bonus exercises

**B1.** One of the first things we look for in new genomes is evidence for conserved protein sequences. In this group of Deltaproteobacteria, genes for mercury methylation (*hgcAB*) are common (for further reading, see links to papers below). Methylmercury is more toxic than mercury because the methyl group makes it lipophilic, and so methylmercury bioaccumulates as it moves up aquatic food chains. Do a `grep` search - do you see any evidence for mercury methylation genes? Be creative with your search terms, and remember that **absence of evidence is not evidence of absence.**

A more robust approach is to look at the amino acid sequences themselves and search for homologous proteins. The tool for this is called BLAST (Basic Local Alignment Search Tool). To use BLAST, we first need to make a database of our annotated proteins (modify as necessary if you are in an even-numbered group). Remember, always tab-autocomplete!

    cd
    makeblastdb -in Dsoud_annotation/Dsoud.faa -out Dsoud_blastdb -dbtype prot

Then, run BLAST using the file of HgcAB protein sequences provided:

    blastp -query hgcAB.faa -db Dsoud_blastdb -out blastp_results.txt
    
Examine the output file. Does your genome contain genes encoding *HgcAB* homologs? If so, write their locus tags to the Etherpad.

**B2.**  The `.gff` (general feature format) file contains information about whether a gene is on the plus or minus strand. Using your Unix cheat sheet, write a command to count the number of genes on each DNA strand.  **Hint:** use the Unix pipe `|` to take the output from one command and redirect it into another. How can you avoid incorrectly counting lines that have a `+` or `-` in the protein name?

# FAQ
**Why are we connecting to a remote computer? Can't we use the computer we have?**
In theory, yes. But there are a few key reasons why we're using remote computers:

*   Prokka (the genome annotation tool we are using) requires several additional tools already be installed (called *dependencies*), and it is not trivial to install them
*   Reproducibility: I created a "snapshot" (called an Amazon Machine Instance, or AMI) of a computer with Prokka and its dependencies already installed. Therefore each student is using the same software in the same computing environment, which guarantees that everyone's results will be reproducible.
*   You can't break a remote machine. Even if something goes horribly wrong, you can be back up to speed in minutes by simply re-launching another Amazon instance.

**What do I do if I get a `broken pipe` error message?**
This happens occasionally when the network connection between your local computer and the remote server is lost. To reconnect to the remote instance, in the Mac Terminal, press the up arrow key to re-run the last `ssh student@ec2...` command.

**Where did the genomes come from?** The two *Desulfuromonas* genomes used in today's lesson were sequenced by Jon and came from a half mile underground in the [Soudan Iron Mine](http://www.dnr.state.mn.us/state_parks/lake_vermilion_soudan/index.html). Both organisms make a living by respiring extracellular metals instead of oxygen.

# Papers related to today's lab
*   Seemann T. 2014. Prokka: rapid prokaryotic genome annotation. *Bioinformatics* **11**:119.
*   Hyatt D et al. 2010. Prodigal: prokaryotic gene recognition and translation initiation site identification. *BMC Bioinformatics* **30**:2068-2069.
*   Parks JM et al. 2013. The genetic basis for bacterial mercury methylation. *Science* **339**:1332–1335.
*   Podar M et al. 2015. Global prevalence and distribution of genes and microorganisms involved in mercury methylation. *Science Advances* **1**:e1500675–e1500675.

[Link to PDF files](http://ow.ly/TEBQ3)

# Other links
*   [Unix shell cheat sheet handout](https://www.dropbox.com/s/h6rousr0rc5ixwo/Unix_shell_cheat_sheet.pdf?dl=0)
*   [Evolution of DNA Sequencing](https://www.youtube.com/watch?v=s9UbA7VyISQ) - seminar by Jonathan Eisen

# About/Contact
This lesson was created by Jon Badalamenti (**email** jonb *\[at\]* umn *\[dot\]* edu  |  **twitter** @jbadomics), University of Minnesota, October 2015.
