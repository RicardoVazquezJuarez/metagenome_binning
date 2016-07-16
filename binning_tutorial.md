
#Metagenome binning using MetaBAT
Authored by Fan Yang 
[EDAMAME-2016 wiki](https://github.com/edamame-course/2016-tutorials/wiki)

***
EDAMAME tutorials have a CC-BY [license](https://github.com/edamame-course/2015-tutorials/blob/master/LICENSE.md). _Share, adapt, and attribute please!_
***

##Overarching Goal
* This tutorial will contribute towards an understanding of **microbial metagenome analysis**

##Learning Objectives
* Understand metagenomic binning and perform a binning exercise

##Tutorial
We will be running a specific tool for metagenomic binning called MetaBAT.  You can read about this tool and its applications in the original paper, [MetaBAT, an efficient tool for accurately reconstructing single genomes from complex microbial communities] (https://peerj.com/articles/1165/)

Let's start by getting our computer!

1. Initiate an Amazon EC2 instance if you don't have one launched already   
   a. ubuntu   
   b. m3.large    

Now, let's download our software.

2. Where to get MetaBAT?    
   A: MetaBAT is deposited in BitBucket (remote repository like GitHub)
   ```
   cd ~
   sudo apt-get install git    ## EC2 does not have git installed by default
   ```
   ```
   git clone https://bitbucket.org/berkeleylab/metabat.git metabat_clone
   git clone https://github.com/edamame-course/metagenome_binning
   ```
   
3. How to install MetaBAT? Do I have all softwares I need to run MetaBAT?
   ```
   cd metabat_clone
   less README.md
   less INSTALL.md
   ```
   Don't forget to push "q" key to exist the "less" window.


4. Install MetaBAT
   ```
   cd ~
   bash ~/metagenome_binning/installation.sh
   ```

This tutorial takes place assuming you have an assembled metagenome and its mapped read abundances.

5. So you learned about how to assemble sequences yesterday and assembled some contigs, 
for today's tutorial, we need a full assembled file. 
   Let's also get some assembled and mapped data. 
   ```
   cd ~
   mkdir data && cd ~/data
   wget https://s3.amazonaws.com/edamame/SRR492066.sam.bam.sorted.bam
   wget https://s3.amazonaws.com/edamame/SRR492065.sam.bam.sorted.bam
   wget https://s3.amazonaws.com/edamame/final.contigs.fa
   ```
Q: Can you think of a reason why we couldn't use the partial assembled contigs from the metagenome assembly exercise? 

6. Now let's do some binning!
    ```
    cd ~/data

    for i in *.sorted.bam 
	do 
		mkdir ~/data/$i.bins 
                cd ~/data/$i.bins
                ~/metabat/bin/runMetaBat.sh ~/data/final.contigs.fa ~/data/$i
        done 
    ```

7. What if we want to bin the contigs with different threshold?
    ```
    cd ~/data/SRR492065.sam.bam.sorted.bam.bins
    #First, try sensitive mode to better sensitivity
    ~/metabat/bin/metabat -i ~/data/final.contigs.fa -a final.contigs.fa.depth.txt -o bin1 --sensitive -l -v --saveTNF saved.tnf --saveDistance saved.gprob

    #Try specific mode to improve specificity further; this time the binning will be much faster since it reuses saved calculations
    ~/metabat/bin/metabat -i ~/data/final.contigs.fa -a final.contigs.fa.depth.txt -o bin2 --specific -l -v --saveTNF saved.tnf --saveDistance saved.gprob

    #Try specific mode with paired data to improve sensitivity while minimizing the loss of specificity
    ~/metabat/bin/metabat -i ~/data/final.contigs.fa -p final.contigs.fa.paired.txt -o bin3 --specific -l -v --saveTNF saved.tnf --saveDistance saved.gprob
    ``` 
