Lab 5: Trimming
--


During this lab, we will acquaint ourselves with the the software packages FastQP. You will use a m1.medium machine.  Your objectives are:



1. Familiarize yourself with the software, how to execute it, how to visualize results.

2. Regarding your dataset. Characterize sequence quality.


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



> So now that we have updates the software, let's see how to add new software. Same basic command, but instead of the `update` or `upgrade` command, we're using `install`. EASY!!


```
sudo apt-get -y install build-essential python python-pip gdebi-core r-base
```


> Install Conda.

```
mkdir anaconda
cd anaconda
curl -LO https://repo.anaconda.com/archive/Anaconda3-5.1.0-Linux-x86_64.sh
bash Anaconda3-5.1.0-Linux-x86_64.sh -b -p $HOME/anaconda/install/
echo ". $HOME/anaconda/install/etc/profile.d/conda.sh" >> ~/.bashrc
source ~/.bashrc
```

> Make a conda environment, activate it, and install trimmomatic, and jellyfish. Make sure you know what these software packages are used for.

```
conda update -y -n base conda
conda create -y --name gen711
conda activate gen711
conda install -y -c bioconda trimmomatic jellyfish
```

> install something via pip. Pip is the python package manager.

```
pip install --upgrade pip
pip install fastqp
```

> Download data. For this lab, we'll be using only 1 sequencing file, which contains 1 million reads. This is a tiny dataset.

```
cd
curl -LO https://s3.amazonaws.com/gen711/1.subsamp_1.fastq
```

---

> Do 3 different trimming levels - 2, 10, 30. This one is trimming at a Phred score of 30 (BAD!!!) PAY ATTENTION HERE!!! When you run the other commands, you'll need to change the numbers in `LEADING:30` `TRAILING:30` `SLIDINGWINDOW:4:30` and `reads.trim.Phred30.fastq` from 30 to whatever trimming level you are using. Use a text editor (not Word) to do this. BBEdit on a mac, or TextEdit on PC are good choices.


>paste the below lines together as 1 command. you will need to change the numbers and run 2 more times after this 1st time.

```
trimmomatic SE -threads 6 \
1.subsamp_1.fastq \
reads.trim.Phred30.fastq \
ILLUMINACLIP:$HOME/anaconda/install/envs/gen711/share/trimmomatic-0.38-1/adapters/TruSeq3-PE.fa:2:30:10 \
SLIDINGWINDOW:4:30 \
LEADING:30 \
TRAILING:30 \
MINLEN:25
```

> After Trimmomatic is done, Run FastQP. FastQP makes some figures that help you understand the quality of your reads, and the impact of your trimming.


```
fastqp -n 500000 1.subsamp_1.fastq -o notrim 2> /dev/null | grep q50 | tee -a qual.P0.txt
fastqp -n 500000 reads.trim.Phred2.fastq -o p2trim 2> /dev/null | grep q50 | tee -a qual.P2.txt
fastqp -n 500000 reads.trim.Phred10.fastq -o p10trim 2> /dev/null | grep q50 | tee -a qual.P10.txt
fastqp -n 500000 reads.trim.Phred30.fastq -o p30trim 2> /dev/null | grep q50 | tee -a qual.P30.txt
```


> Download your results files to your local computer. Do this in a new tab if you are working on a Mac, or sign out from your instance if you are working on a PC. You will need to edit this command, then find the files on your laptop (they should go to Desktop), extract them and view.

```
rsync -av -e "ssh -i $HOME/jetkey" mmacmane@129.114.16.110:*zip ~/Desktop/
```

## Terminate your instance
