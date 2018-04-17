# Day 2 Tutorials
---
If you do no wish to run Linux from a USB drive today, you can get by with using Windows. However, you may not be able to do everything below but you will be able to follow along with most things.

Create a directory on your usb drive within phyloWorkshop called day2Practical.  Substitute the name of your USB drive below.  

  ```mkdir /media/ubuntu/NameOfUSBDrive/phyloWorkshop/day2Practical```  

Save all files you create today in the directory above.


Boot in linux and run the install.phylo.day2.sh script to get started. Also, you will need to paste the text from install.efetch.txt into your terminal. If you left the linux USB drive in your computer and did not shut it down, you do not have to reinstall everything again.  


# Aligning nucleotide sequences with mafft Online
 We will use the online version of mafft to align nucleotide sequences from closely related taxa and distantly related taxa.

 **Acquiring sequences from representative taxa across the Ascomycota**

---
1. Using esearch and efetch, grab the following sequences from GenBank in fasta format and save to a file called ```ascos.its.fasta```. Refer to your notes from yesterday if you do not recall how to use esearch and efetch.  
  - ```JQ726609 AY773462 AY144561 KC254096 AY930108 DQ683124 JQ993741 EU554664```

2.  Go to https://mafft.cbrc.jp/alignment/software/  
  - On the left side of the screen select "Alignment"
  - I will walk you through how to submit an alignment. Open a new tab for each alignment.

3. Now that you have submitted an alignment, submit additional alignment jobs and adjust the alignment parameters.
  - Using the ascos.its.fasta file, run the following alignments. Open a new tab in your browser for each alignment.
    - FFT-NS-1; leave all else as default; select fasta output; copy and paste into text file; save as ```ascos.its.mafft.FFT-NS-1.aln```
    - G-INS-i; leave all else as default; save as ```ascos.its.mafft.GINS-i.aln```
    - E-INS-i; leave all else default; save as ```ascos.its.mafft.EINS-i.aln```
    - G-INS-i; change the gap opening penalty to 5.0; save as ```ascos.its.mafft.GINS-i.op5.aln```
    - G-INS-i; change "Scoring matrix for nucleotide sequences" from 200PAM/k=2 to 1PAM/k=2; make sure gap opening penalty is set back to 1.53; save as ```ascos.its.mafft.GINS-i.1PAM.aln```

Do these alignments differ in length?

Do you think one alignment is better than the others? Is one obviously worse than the others?

Can you improve the alignment by making manual adjustments? Does this feel objective?
  - Look at the "Align" menu in Aliview for how to manually edit alignments. **Quick in-class demo.**

# Minimizing Alignment Uncertainty
---
As you can see from the above alignment, there can be a lot of uncertainty in the alignment process and dealing with that uncertainty by manually adjusting alignments is not ideal.

We are going to use a couple different approaches to dealing with alignment uncertainty: Guidance2 and GBLOCKS.

## Minimizing Alignment Uncertainty with GUIDANCE2

Guidance is provides a statistical approach to removing uncertain regions of the alignment and allows a flexible framework for user-defined filtering. Here is the description from the Guidance website:  
```"The GUIDANCE web-server is a powerful and user-friendly tool for assigning a confidence score for each residue, column, and sequence in an alignment and for projecting these scores onto the MSA [1]. The server points to columns and sequences that are unreliably aligned and enables their automatic removal from the MSA, in preparation for downstream analyses."```

```"Three algorithms for quantifying MSA uncertainties are implemented in the server. The GUIDANCE score is based on the robustness of the MSA to guide-tree uncertainty and relies on the bootstrap approach [2]. The Heads-or-Tails (HoT) score measures alignment uncertainty due to co-optimal solutions [3]. GUIDANCE2 is an integrative methodology that accounts for: (1) uncertainty in the process of indel formation; (2) uncertainty in the assumed guide tree (as GUIDANCE); (3) co-optimal solutions in the pair-wise alignments, used as building blocks in progressive alignment algorithms (as HoT)."```

**Running GUIDANCE**  
1. From the results page of MAFFT, you will see a radio button that will allow you to direct the multiple sequence alignment output by MAFFT to the GUIDANCE server. Open the tab in your browser for your GINS-i alignment (ascos.its.mafft.GINS-i.aln).
2. Click on the ```GUIDANCE2``` radio button. Click OK in the popup dialog box.
3. You should be directed to the GUIDANCE2 server and your alignment should appear. Make sure that GUIDANCE2 is the selected algorithm. Enter your email address and give the task a name that is descriptive (ascos.its.mafft.GINS-i.aln.guidance2).
4. Leave the bootstrap repeats at the default of 100.
5. Type in the characters from the picture at the bottom to prove you are not a robot.

While that is running, we will move on to looking at how to use GBLOCKS. We will come back to talk about the results from GUIDANCE2.





## Minimizing Alignment Uncertainty with GBLOCKS

While GUIDANCE2 takes an inferential approach to estimating alignment uncertainty, GBLOCKS takes an algorithmic approach. This means that it modifies the alignment based on a set of rules. It should produce the exact same modified alignment if you run the same alignment under the same set of conditions. Here is a description from the GBLOCKS website.

```"Gblocks is a computer program written in ANSI C language that eliminates poorly aligned positions and divergent regions of an alignment of DNA or protein sequences. These positions may not be homologous or may have been saturated by multiple substitutions and it is convenient to eliminate them prior to phylogenetic analysis. Gblocks selects blocks in a similar way as it is usually done by hand but following a reproducible set of conditions. The selected blocks must fulfill certain requirements with respect to the lack of large segments of contiguous nonconserved positions, lack or low density of gap positions and high conservation of flanking positions, making the final alignment more suitable for phylogenetic analysis. Gblocks outputs several files to visualize the selected blocks. The use of a program such as Gblocks reduces the necessity of manually editing multiple alignments, makes the automation of phylogenetic analysis of large data sets feasible and, finally, facilitates the reproduction of the alignments and subsequent phylogenetic analysis by other researchers."```

```"Several parameters can be modified to make the selection of blocks more or less stringent. In general, a relaxed selection of blocks is better for short alignments, whereas a stringent selection is more adequate for longer ones. Be aware that the default options of Gblocks are stringent."```

You will generally need to edit the names of sequences in fasta files acquired from GenBank in order to conform to the format requirements of various sequence analysis software. In general, you want to avoid hyphens (-) because these are interpreted as indels. You also want to avoid spaces in taxon names because everything after the first space is interpreted as data and this will cause most software to give an error of some kind. GBLOCKS is the first such software we have encountered, so we will edit our fasta files. Renaming fasta files is a tedious and boring process, so I have written a python script to do it for us. It will change the names to the following format: ```GenBankAccessionNumber_Genus_species``` It should have been installed when you ran the install.phylo.day2.sh script.

See if it is installed: ```reformatFasta.py```
```
Traceback (most recent call last):
  File "./reformatFasta.py", line 7, in <module>
    newFile = open(sys.argv[2], 'w')
IndexError: list index out of range
```

Open the terminal and change into the directory where the fasta files are that you want to edit. Substitute the name of your drive for NameOfUSBDrive below to enter the correct file path. Let me know if you are having problems.

```cd /media/ubuntu/NameOfUSBDrive/phyloWorkshop/day1Practical```
reformatFasta.py```

See how to use reformatFasta.py by looking at the first few lines of the script. ```head -n 4 reformatFasta.py```  
```
#! /usr/bin/env python

#USAGE: python reformatFasta.py NameOfInput(fasta - output from genbankProcess.py) NameOfOutputFile(fasta)
```
```python reformatFasta.py ascos.its.mafft.GINS-i.fasta ascos.its.mafft.GINS-i.mod.fasta```  

Compare the first line of the files before and after reformatting to check:  
```head -n 1 ascos.its.mafft.GINS-i.fasta```  

```
>JQ726609.1 Saccharomyces cerevisiae 18S ribosomal RNA gene, partial sequence; internal transcribed spacer 1, 5.8S ribosomal RNA gene, and internal transcribed spacer 2, complete sequence; and 28S ribosomal RNA gene, partial sequen
```

```head -n 1 ascos.its.mafft.GINS-i.mod.fasta```   

```
>JQ726609_Saccharomyces_cerevisiae
```

Now we can proceed with running GBLOCKS.

**Running GBLOCKS**

1. Open a browser to http://molevol.cmima.csic.es/castresana/Gblocks_server.html
2. Upload your alignment that was processed by reformatFasta.py (ascos.its.mafft.GINS-i.mod.fasta)
3. Select DNA as the type of sequence.
4. Select all three options for the "less stringent selection".
5. Click the ```Get Blocks``` radio button.

The results will be generated very quickly. The first screen that pops up will show your original alignment. We will walk through the results together.

It will also be worthwhile to look at the methods together.   http://molevol.cmima.csic.es/castresana/Gblocks/Gblocks_documentation.html

It is always best practice to record the parameters that were used. "Default" parameters can change over time for software, so write down the parameters at the bottom of the page for each alignment.

**Obtaining Results**  
1. Click on "Resulting Alignment" at the bottom.
2. Highlight the alignment (Select All).
3. Copy and paste into a new text file.
4. Save as ascos.its.mafft.GINS-i.mod.gb.lessStringent.fasta

**Repeat the processing of ascos.its.mafft.GINS-i.mod.fasta with GBLOCKS default parameters**  
1. Upload your alignment.
2. Select DNA  
3. Do not check any boxes.
4. Get Blocks.
5. Write down the parameters used.
5. Save alignment as ascos.its.mafft.GINS-i.mod.gb.default.fasta

Compare the number of positions between the two alignments. Look at each in Aliview.

Do you feel more confidence in the alignments resulting from GBLOCKS and GUIDANCE2?

Do you need to adjust the alignment manually?

** If we have time: **  
Move on to model-testing tutorial.
