Metagenome binning using MetaBAT
---

[MetaBAT, an efficient tool for accurately reconstructing single genomes from complex microbial communities] (https://peerj.com/articles/1165/)

1. Initiate an Amazon EC2 instance if you don't have one launched already   
   a. ubuntu   
   b. m3.large    

2. Where to get MetaBAT?    
   A: MetaBAT is deposited in BitBucket (remote repository like GitHub)
   ```
   cd ~
   sudo apt-get install git    ## EC2 does not have git installed by default
   git clone https://bitbucket.org/berkeleylab/metabat.git metabat_clone
   git clone https://github.com/edamame-course/metagenome_binning
   ```
   
3. How to install MetaBAT? Do I have all softwares I need to run MetaBAT?
   ```
   cd metabat_clone
   less README.md
   less INSTALL.md
   ```

4. Install MetaBAT
   ```
   cd ~
   bash ~/metagenome_binning/installation.sh
   ```

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
    cd ~/data/SRR492065.sam.bam.sorted.bam.bin
    #First, try sensitive mode to better sensitivity
    ~/metabat/bin/metabat -i ~/data/final.contigs.fa -a final.contigs.fa.depth.txt -o bin1 --sensitive -l -v --saveTNF saved.tnf --saveDistance saved.gprob

    #Try specific mode to improve specificity further; this time the binning will be much faster since it reuses saved calculations
    ~/metabat/bin/metabat -i ~/data/final.contigs.fa -a final.contigs.fa.depth.txt -o bin2 --specific -l -v --saveTNF saved.tnf --saveDistance saved.gprob

    #Try specific mode with paired data to improve sensitivity while minimizing the loss of specificity
    ~/metabat/bin/metabat -i ~/data/final.contigs.fa -p final.contigs.fa.paired.txt -o bin3 --specific -l -v --saveTNF saved.tnf --saveDistance saved.gprob
    ``` 
