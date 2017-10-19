Lab 7: khmer
--

During this lab, we will acquaint ourselves with digital normalization. You will:

1. Install software and download data

2. Quality and adapter trim data sets.

3. Apply digital normalization to the dataset.

4. Count and compare kmers and kmer distributions in the normalized and un-normalized dataset.

5. Plot in RStudio.


The JellyFish manual: ftp://ftp.genome.umd.edu/pub/jellyfish/JellyfishUserGuide.pdf
The Khmer manual: http://khmer.readthedocs.org/en/v1.1
Skewer: http://www.biomedcentral.com/1471-2105/15/182
Seqtk: https://github.com/lh3/seqtk


> Step 1: Launch, For this exercise, we will use a xlarge instance.



> Install other software, including RStudio

```
echo "deb https://cloud.r-project.org/bin/linux/ubuntu xenial/" | sudo tee -a /etc/apt/sources.list
sudo apt-get update
sudo apt-get -y --allow-unauthenticated install ruby build-essential python python-pip gdebi-core r-base
curl -LO  https://download2.rstudio.org/rstudio-server-1.0.143-amd64.deb
sudo gdebi -n rstudio-server-1.0.143-amd64.deb
```
> Install LinuxBrew, and then the following software.

```
gcc jellyfish skewer seqtk

```

> Install Khmer

```
    pip install khmer
```
> Download data and a file with the Illumina adapters, ``TruSeq3-PE.fa``

::
```
  curl -LO https://s3.amazonaws.com/gen711/TruSeq3-PE.fa
  curl -L https://s3.amazonaws.com/Mc_Transcriptome/Thomas_McBr1_R1.PF.fastq.gz > kidney.1.fq.gz &
  curl -L https://s3.amazonaws.com/Mc_Transcriptome/Thomas_McBr1_R2.PF.fastq.gz > kidney.2.fq.gz
```

> Merge --> Trim low quality bases and adapters from dataset  --> count kmers --> make a histogram. Normalize in the 1nd command. Make sure you know what is going on here!

::



```
trim=2
norm=30

#paste the below lines together as 1 command

seqtk mergepe kidney.1.fq.gz kidney.2.fq.gz \
| skewer -l 25 -m pe --mean-quality $trim --end-quality $trim -t 8 -x $HOME/TruSeq3-PE.fa - -1 \
| jellyfish count -m 25 -s 700M -t 8 -C -o /dev/stdout /dev/stdin \
| jellyfish histo /dev/stdin -o trimmed.no.normalize.histo

#and

#paste the below lines together as 1 command

seqtk mergepe kidney.1.fq.gz kidney.2.fq.gz \
| skewer -l 25 -m pe --mean-quality $trim --end-quality $trim -t 8 -x $HOME/TruSeq3-PE.fa - -1 \
| normalize-by-median.py --max-memory-usage 4e9 -C $norm -o - - \
| jellyfish count -m 25 -s 700M -t 8 -C -o /dev/stdout /dev/stdin \
| jellyfish histo /dev/stdin -o trimmed.yes.normalize.histo
```

> Now, you ahve 2 files: `trimmed.yes.normalize.histo` and `trimmed.no.normalize.histo`. these contain the kmer frequency data. The 1st column is the abundance, the 2nd column is the number of 25mers that have that abundance. Can you tell me how many unique kmers there are in each dataset? Does this make sense?  


> Launch RStudio, remember you have to make a new password, and find the web address. See lab 1 for details. 

```
install.packages("beanplot")
library("beanplot")

#Import all 2 histogram datasets: this is the code for importing 1 of them..


y_khmer <- read.table("trimmed.yes.normalize.histo", quote="\"")
n_khmer <- read.table("trimmed.no.normalize.histo", quote="\"")

# plot differences between non-unique kmers

plot(y_khmer$V2[0:300] - n_khmer$V2[0:300], type='l',
    xlim=c(0,300), xaxs="i", yaxs="i", frame.plot=F,
    ylim=c(-20000,20000), col='red', xlab='kmer frequency',
    lwd=4, ylab='count',
    main='Diff in 25mer counts of \n normalized vs. un-normalized datasets')
abline(h=0)
```


> What do the analyses of kmer counts tell you?


TERMINATE YOUR INSTANCE
--
