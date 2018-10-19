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



> Step 1: Launch, For this exercise, we will use a m1.large instance.



> Install other software, including RStudio

```
sudo apt-get -y install build-essential python python-pip gdebi-core r-base
curl -LO  https://download2.rstudio.org/rstudio-server-1.0.143-amd64.deb
sudo gdebi -n rstudio-server-1.0.143-amd64.deb
```
> Install Conda, and then the following software.

```
khmer jellyfish trimmomatic

```

> Download data


```
cd
curl -L https://s3.amazonaws.com/Mc_Transcriptome/Thomas_McBr1_R1.PF.fastq.gz > kidney.1.fq.gz
```

> Merge --> Trim low quality bases and adapters from dataset  --> count kmers --> make a histogram. Normalize in the 2nd command. Make sure you know what is going on here!

::



```
trim=2
norm=30

#paste the below lines together as 1 command


trimmomatic SE -threads 10 kidney.1.fq.gz /dev/stdout \
ILLUMINACLIP:$HOME/anaconda/install/envs/gen711/share/trimmomatic-0.38-1/adapters/TruSeq3-PE.fa:2:30:10 \
SLIDINGWINDOW:4:2 LEADING:2 TRAILING:2 MINLEN:25 \
| jellyfish count -m 25 -s 700M -t 8 -C -o /dev/stdout /dev/stdin \
| jellyfish histo /dev/stdin -o trimmed.no.normalize.histo

#and

#paste the below lines together as 1 command

trimmomatic SE -threads 10 kidney.1.fq.gz /dev/stdout \
ILLUMINACLIP:$HOME/anaconda/install/envs/gen711/share/trimmomatic-0.38-1/adapters/TruSeq3-PE.fa:2:30:10 \
SLIDINGWINDOW:4:2 LEADING:2 TRAILING:2 MINLEN:25 \
| normalize-by-median.py --max-memory-usage 1e9 -C $norm -o - - \
| jellyfish count -m 25 -s 700M -t 8 -C -o /dev/stdout /dev/stdin \
| jellyfish histo /dev/stdin -o trimmed.yes.normalize.histo
```

> Now, you have 2 files: `trimmed.yes.normalize.histo` and `trimmed.no.normalize.histo`. these contain the kmer frequency data. The 1st column is the abundance, the 2nd column is the number of 25mers that have that abundance. Can you tell me how many unique kmers there are in each dataset? Does this make sense?  


> Launch RStudio, remember you have to make a new password, and find the web address. See lab 2 (blast lab) for details.

```
install.packages("beanplot")
library("beanplot")

#Import all 2 histogram datasets:


y_khmer <- read.table("trimmed.yes.normalize.histo", quote="\"")
n_khmer <- read.table("trimmed.no.normalize.histo", quote="\"")

# plot differences between non-unique kmers

par(mfrow=c(2,2))

plot(y_khmer$V2[0:300] - n_khmer$V2[0:300], type='l',
    xlim=c(0,300), xaxs="i", yaxs="i", frame.plot=F,
    ylim=c(-20000,20000), col='red', xlab='kmer frequency',
    lwd=4, ylab='count',
    main='Diff in 25mer counts of \n C20 normalized vs. un-normalized datasets')
abline(h=0)

plot(y_khmer$V2[0:100], type='l',
      xlim=c(0,100), xaxs="i", yaxs="i", frame.plot=F,
      ylim=c(0,180000), col='red', xlab='kmer frequency',
      lwd=4, ylab='count',
      main='Kmer distribution of C20-normalized dataset')

plot(n_khmer$V2[0:100], type='l',
      xlim=c(0,100), xaxs="i", yaxs="i", frame.plot=F,
      ylim=c(0,180000), col='red', xlab='kmer frequency',
      lwd=4, ylab='count',
      main='Kmer distribution of non-normalized dataset')

```


> What do the analyses of kmer counts tell you?


TERMINATE YOUR INSTANCE
--
