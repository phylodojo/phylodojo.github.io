# Day 3 Tutorials
---
Review GUIDANCE results from day 2.
---
If you do not wish to run Linux from a USB drive today, you can get by with using Windows. However, you may not be able to do everything below but you will be able to follow along with most things.

Create a directory on your usb drive within phyloWorkshop called day3Practical. Substitute the name of your USB drive below.
mkdir /media/ubuntu/NameOfUSBDrive/phyloWorkshop/day3Practical

Save all files you create today in the directory above.

Boot in linux and run the install.phylo.day3.sh script to get started. If you left the linux USB drive in your computer and did not shut it down, you do not have to reinstall everything again, but you will need to install jModeltest2. We can grab those lines from the install script. Let me know if this case applies to you and I will assist.

# Finding the best-fit model of sequence evolution

We are going to follow this tutorial on jModeltest2 [here](http://evomics.org/learning/phylogenetics/jmodeltest/) while making some modifications. jModeltest2 should have been installed when running the install.phylo.day3.sh script.

Modifications:
1. Use the file you exported from GUIDANCE in fasta format instead of " percidae.fasta", as specified in the tutorial.
2. We will just focus on model selection using AIC corrected for small sample sizes (AICc) and BIC. As noted in the tutorial, AICc approximates AIC when sample sizes become large, so just use AICc for any dataset.
3. We will not worry about model averaged estimates of parameters and phylogenies.

# Maximum Likelihood Phylogenetic Analysis

We will be exploring the use of two different programs for maximum likelihood phylogenetic analysis: Garli and RAxML (if time permits). Why two programs? The options for model implementation differ between Garli and RAxML. All of the models that jModeltest2 compares can be implemented in Garli, however RAxML is limited in options. RAxML is generally much faster, particularly for large datasets, but if you are interested in looking at the impact of specific models on phylogenetic analysis, prefer to implement the best-fit models selected by modeltesting, or you want to run different nucleotide/amino acid substitution models for different partitions of the data, you will want to use Garli.

### GARLI_Configuration_settings

From the digitalworldbiology website:  
_```GARLI, Genetic Algorithm for Rapid Likelihood Inference is a program for inferring phylogenetic trees. Using an approach similar to a classical genetic algorithm, it rapidly searches the space of evolutionary trees and model parameters to find the solution maximizing the likelihood score. It implements nucleotide, amino acid and codon-based models of sequence evolution, and runs on all platforms. The latest version adds support for partitioned models and morphology-like datatypes. It is written and maintained by Derrick Zwickl.```_

Running Garli requires a nexus file and a garli.conf file to execute.

1. We will start by converting our fasta file that we ran with jModeltest2 into a nexus file. Download seqConverter.pl from https://github.com/phylodojo/files [To do so, click on the filename, then right-click on RAW and 'Save Link As']. Save to your USB drive, then copy into the same directory as your fasta file.  
With both seqConverter.pl and your fasta file in the same directory, type the following on the command line:  
  - ```./seqConverter.pl -dascos.its.mafft.GINS-i.mod.fasta -if -on -ri```  
  - To see the help menu for seqConverter.pl: ```./seqConverter.pl -h```  
  - seqConverter.pl will output a file called ```ascos.its.mafft.GINS-i.mod.nexus```

  **If you are using Windows, you can use NCLconverter on CIPRES to convert your file to nexus. Alternatively, you can use the webserver ALTER: http://sing.ei.uvigo.es/ALTER/**  


2. Now, let's setup a directory to put the necessary files for running our Garli analyses.  
  - ```mkdir GarliAnalyses```  
  - ```cp ascos.its.mafft.GINS-i.mod.nexus GarliAnalyses``` **You can also mv the file instead of copying, but have to be very careful when using mv or rm commands, because you can easily delete the file permanently.**

3. Download a template garli.conf file from https://github.com/phylodojo/files.  
  - Click on garli.cipres.single.conf; right-click on "RAW" and "Save Link As". Save file into GarliAnalyses
  - Open garli.cipres.single.conf in a text editor (search for gedit on Ubuntu)

4. Setup garli.cipres.single.conf for ML analysis.  
We will go over the parameter settings and setup the garli.conf file for ML analysis. It will be helpful to refer to this page: https://github.com/zwickl/garli/wiki/GARLI_Configuration_settings
  - Be sure to set the availablememory to 2000. Cipres limitation.

5. We will set the model in garli.cipres.single.conf based on the results from jModeltest2.  
Referring to this page will be helpful: https://github.com/zwickl/garli/wiki/FAQ#MODELTEST_told_me_to_use_model_X_How_do_I_set_that_up_in_GARLI

6. Setup a new garli.conf file for bootstrap analysis.  
  - Create a directory inside GarliAnalyses called bootstrap.  
    - ```mkdir bootstrap```
    - Copy your existing garli.cipres.single.conf into bootstrap: ```cp garli.cipres.single.conf boostrap/```
    - Open the copy of garli.cipres.single.conf that is in the bootstrap directory with gedit.
    - We will only change three parameters in the garli.cipres.single.conf file for the bootstrap analyses: ```searchreps``` and ```bootstrapreps``` and ```genthreshfortopoterm```. In general, for bootstrapping we will want to reduce searchreps to 1 or 2, bootstrap reps to 500-1000, and genthreshfortopoterm to half or 75% of the original (10,000-15,000), depending on the complexity of the likelihood surface. **However, bootstrapping can take a long time. Therefore, we are going to setup two different bootstrap configuration files. The first will run 10 bootstrap reps (not nearly enough for estimating branch support) and the second with 500 bootstrap reps (just barely enough).**
    - After making these changes, save these files under a new name: garli.boot.10.cipres.conf and garli.boot.500.cipres.conf

**A note about Cipres. Cipres runs each of the searchreps independently so as to use computational resources more efficiently. This is good for getting your results quickly, but it makes it more difficult to compare trees across searchreps. I will show you a screen.log file from the multi-population version of Garli so you can see what the output is like. In the case of the Cipres version, we will map bootstrap support onto the tree with the highest likelihood without worrying about how many times that particular topolgy was found across search reps. However, that is a comparison that you would want to make. You can load the trees into PAUP and see if the topology is the same by calculating the Robinson-Foulds distance between trees.**

7. We will walk how to submit the maximum likelihood and bootstrap analyses on Cipres together. We will have to wait for the analyses to finish before moving on, but this should not take long.

### Mapping support values onto the best trees

We are going to use a python library for phylogenetic computing called Dendropy to map support values from our bootstrap trees onto our best tree from the ML analysis. This is straightforward to install on Linux and if you ran the install script for day 3, you should already have it installed. Type the following into the terminal to see if you have it: ```sumtrees.py```  
The output should like the following if it is installed.  
```
======================================================================
SumTrees - Phylogenetic Tree Split Support Summarization
Version 3.3.1 (May 05 2011)
By Jeet Sukumaran and Mark T. Holder
(using the DendroPy Phylogenetic Computing Library Version 3.12.0)
======================================================================

SumTrees: No sources of input trees specified. Please provide the path to at
          least one (valid and existing) file containing tree samples to
          summarize. See '--help' for other options.
```  

You can install Dendropy on your Windows machine, but we will not spend time doing this today. You can find instructions for doing so if you look in the section called "SumTrees / Python / DendroPy" at the following url: http://www.peter.unmack.net/molecular/programs/garli.instructions.html  

If you have trouble installing it and you would like to do so, please ask me on Friday during the consultation period for assistance. In the meantime, get with someone that has it installed on Linux.

1. We will look at the output files from the ML analysis on Cipres together. You will want to download the files that end in best.tre and garli_scores.txt. The second file will show us the likelihood scores for each replicate analysis so that we know which tree has the highest likelihood score. That is the tree we will want to map support values onto.

2. Now, lets look at the output files from the bootstrap analysis. You will want to download all of the files that end in boot.tre.

3. Make sure the boot.tre files are in the same directory/folder as the best.tre file that represents the tree with the best likelihood score. Using sumtrees.py, summarize the **split frequencies** and map those frequencies to the best tree. Assuming your files are in /home/ubuntu/GarliAnalyses, issue the following commands in the terminal:  
  - ```sumtrees.py  --percentages --no-summary-metadata --decimals=0 -o ascos.its.mafft.GINS-i.best.support.tre -t *.best.tre  *boot.tre```

  - If you want to see the options that are available in sumtrees: ```sumtrees.py -h```
  - If you are using Windows, I will map the bootstrap support values for you and upload the annotated tree to github.

4. Now we can open our tree in FigTree. You can download FigTree from here, if you have not already done so: http://tree.bio.ed.ac.uk/software/figtree/
  - We will walk through how to visualize support values, root our tree, modify branch widths, and export.
