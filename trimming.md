Lab 5: Trimming
--


During this lab, we will acquaint ourselves with the the software packages FastQC and JellyFish. Your objectives are:



1. Familiarize yourself with the software, how to execute it, how to visualize results.

2. Regarding your dataset. Characterize sequence quality.

The FastQC manual: <a href="http://www.bioinformatics.babraham.ac.uk/projects/download.html#fastqc">http://www.bioinformatics.babraham.ac.uk/projects/download.html#fastqc</a>

The JellyFish manual: <a href="ftp://ftp.genome.umd.edu/pub/jellyfish/JellyfishUserGuide.pdf">ftp://ftp.genome.umd.edu/pub/jellyfish/JellyfishUserGuide.pdf</a>

> Step 1: Launch an instance on Jetstream. For this exercise, we will use a m1.medium instance.

```
ssh -i jetkey username@xxx.xxx.xxx.xxx
```

> The machine you are using is Linux Ubuntu: Ubuntu is an operating system you can use (I do) on your laptop or desktop. One of the nice things about this OS is the ability to update the software, easily.  The command `sudo apt-get update` checks a server for updates to existing software.


```
sudo apt-get update
```

> The upgrade command actually installs any of the required updates.

```
sudo apt-get -y upgrade
```

> OK, what are these commands?  `sudo` is the command that tells the computer that we have admin privileges. Try running the commands without the sudo -- it will complain that you don't have admin privileges or something like that. *Careful here, using sudo means that you can do something really bad to your own computer -- like delete everything*, so use with caution. It's not a big worry when using Jetstream, as this is a virtual machine- fixing your worst mistake is as easy as just terminating the instance and restarting.


> So now that we have updates the software, lets see how to add new software. Same basic command, but instead of the `update` or `upgrade` command, we're using `install`. EASY!!
> the 1st command tells the computer to look in a different place for updated software, this is needed because of R. We need a newer version than is standard.


```
echo "deb https://cloud.r-project.org/bin/linux/ubuntu xenial/" | sudo tee -a /etc/apt/sources.list
sudo apt-get update
sudo apt-get -y --allow-unauthenticated install ruby build-essential python python-pip gdebi-core r-base

```


> Install LinuxBrew like you have every other week!


> Install gcc (a compiler), mafft (to do alignment) and raxml (to make the phylogeny)

```
brew install gcc jellyfish trimmomatic
pip install fastqp
```


> Download data. For this lab, we'll be using only 1 sequencing file.

```
curl -LO https://s3.amazonaws.com/gen711/1.subsamp_1.fastq
```

---

> Do 3 different trimming levels between 2 and 40. This one is trimming at a Phred score of 30 (BAD!!!) When you run your commands, you'll need to change the numbers in `LEADING:30` `TRAILING:30` `SLIDINGWINDOW:4:30` and `Pero360B.trim.Phred30.fastq` to whatever trimming level you want to use.


>paste the below lines together as 1 command

```
trimmomatic SE -threads 6 \
1.subsamp_1.fastq \
reads.trim.Phred30.fastq \
ILLUMINACLIP:/home/linuxbrew/.linuxbrew/Cellar/trimmomatic/0.36/share/trimmomatic/adapters/TruSeq3-PE.fa:2:30:10 \
SLIDINGWINDOW:4:30 \
LEADING:30 \
TRAILING:30 \
MINLEN:25
```

> After Trimmomatic is done, Run FastQC. You'll have to change the numbers to match the levels you trimmed at.


```
cd $HOME
fastqp 1.subsamp_1.fastq 2> /dev/null | grep q50 > qual.raw.txt
fastqp reads.trim.Phred2.fastq 2> /dev/null | grep q50 > qual.P2.txt
fastqp reads.trim.Phred10.fastq 2> /dev/null | grep q50 > qual.P10.txt
fastqp reads.trim.Phred30.fastq 2> /dev/null | grep q50 > qual.P30.txt
```

> Run Jellyfish.

```
mkdir $HOME/jelly
cd $HOME/jelly
```

> You'll have to run these commands 4 separate times -
> once for each different trimmed dataset, and once for the raw dataset.
> Change the names of the input and output files..

```
jellyfish count -m 25 -s 200M -t 6 -C -o trim2.jf reads.trim.Phred2.fastq
jellyfish histo trim2.jf -o trim2.histo
```

> Now look at the `.histo` file, which is a kmer distribution. I want you to plot the distribution using RStudio.


> OPEN RSTUDIO like you have in the past.

Import all 3 histogram datasets: this is the code for importing 1 of them..

```
trim2 <- read.table("trim2.histo", quote="\"")
```
Import all 3 quality files: this is the code for importing 1 of them..

```
qual0 <- read.table("qual.raw.txt", quote="\"")
```

Plot: Make sure and change the names to match what you import.
What does this plot show you??


```
barplot(c(raw$V2[1],trim2$V2[1],trim10$V2[1],trim30$V2[1]),
         names=c('Raw', 'Phred2', 'Phred10', 'Phred30'),
         main='Number of unique kmers')
```

plot differences between non-unique kmers

```
plot(trim0$V2[2:30] - trim30$V2[2:30], type='l',
    xlim=c(2,20), xaxs="i", yaxs="i", frame.plot=F,
    ylim=c(0,2000000), col='red', xlab='kmer frequency',
    lwd=4, ylab='count',
    main='Diff in 25mer counts of freq 2 to 20 \n Phred2 vs. Phred30')
```

plot the quality scores

```
plot(qual30$V4, type="l", col='red', frame.plot = F, ylab='Quality score', xlab='Position in Read')
lines(qual0$V4, type="l", col='blue')
lines(qual2$V4, type="l", col='green')
lines(qual10$V4, type="l", col='green')
```




> Look at the FastQC plots across the different trimming levels. Anything surprising?

> What do the analyses of kmer counts tell you?

### Terminate your instance
