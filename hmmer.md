Lab3 : HMMER

During this lab, we will acquaint ourselves with the the software package HMMER. Your objectives are:

-

1. Familiarize yourself with the software, how to execute it, how to visualize results.

2. Regarding your dataset. Find Channel proteins. Think about how to make a better HMM.

The HMMER manual <a href="ftp://selab.janelia.org/pub/software/hmmer3/3.1b1/Userguide.pdf">ftp://selab.janelia.org/pub/software/hmmer3/3.1b1/Userguide.pdf</a>

The HMMER webpage: <a href="http://hmmer.janelia.org/">http://hmmer.janelia.org/</a>

---

 Step 1: Launch an instance on Jetstream. For this exercise, we will use a m1.medium instance.

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


```
sudo apt-get -y install build-essential python
```


> Install LinuxBrew. Linux brew is another package manager, but for scientific software. We will use it basically every week!

> Install Conda. Conda is another package manager, but for scientific software. We will use it basically every week!

```
mkdir anaconda
cd anaconda
curl -LO https://repo.anaconda.com/archive/Anaconda3-5.1.0-Linux-x86_64.sh
bash Anaconda3-5.1.0-Linux-x86_64.sh -b -p $HOME/anaconda/install/
echo ". $HOME/anaconda/install/etc/profile.d/conda.sh" >> ~/.bashrc
source ~/.bashrc
```

> Make a conda environment, activate it, and install raxml and mafft. Make sure you know what these software packages are used for.

```
conda create -y --name gen711
conda activate gen711
conda install -y -c bioconda hmmer mafft blast
```


> You will download the mystery dataset, a dataset that contains only sodium channels, and UNIPROT, a protein reference. Do you remember how to use the `curl` commands? FYI, I searched for, and downloaded the channel proteins from http://www.orthodb.org.

```
curl -LO https://www.dropbox.com/s/tzreecjs5d5cj6l/channels.fasta
curl -LO https://www.dropbox.com/s/9kvv4p3x1e3oiju/mystery.fa
curl -LO ftp://ftp.uniprot.org/pub/databases/uniprot/current_release/knowledgebase/complete/uniprot_sprot.fasta.gz
gzip -d uniprot_sprot.fasta.gz
```

> we are going to run HMMER to find Channels in our mystery database. To do this, we 1st need to make a HMM, which takes as input, aligned protein sequences. We need to do an alignment of the channels proteins that you just downloaded.

```
mafft --auto --thread 6 channels.fasta > channels.align.fasta
```

> make the HMM using `hmmbuild`, and then search it using `hmmsearch`

```
hmmbuild channels.hmm channels.align.fasta
hmmsearch --cpu 6 -E 1e-5 --domtblout identified_channel_prots.out channels.hmm mystery.fa
```

> look at `identified_channel_prots.out`

```
cat identified_channel_prots.out | cut -d " " -f1 | grep ENS | sort | uniq | grep --no-group-separator -A1 -w -f - mystery.fa | tee -a maybe-mystery-channels.fasta
```

> check to see if the HMM did a good job, by blasting the proteins that HMMscan identified, to the UniProt database.

```
makeblastdb -in uniprot_sprot.fasta -out uniprot -dbtype prot

blastp -db uniprot -max_target_seqs 1 -query maybe-mystery-channels.fasta \
-outfmt '6 qseqid evalue stitle' -evalue 1e-10 -num_threads 6 -out blast.out

```

Look at the file, `blast.out`. Are their things in there that are not channel proteins (you might have to google)? Why? What can we do to make the results more accurate, if needed?
