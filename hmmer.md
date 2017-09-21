Lab3 : HMMER

During this lab, we will acquaint ourselves with the the software package HMMER. Your objectives are:

-

1. Familiarize yourself with the software, how to execute it, how to visualize results.

2. Regarding your dataset. Characterize a few conserved domains.

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
> the 1st command tells the computer to look in a different place for updated software, this is needed because of R. We need a newer version than is standard.


```
echo "deb https://cloud.r-project.org/bin/linux/ubuntu xenial/" | sudo tee -a /etc/apt/sources.list
sudo apt-get update
sudo apt-get -y --allow-unauthenticated install ruby build-essential python python-pip gdebi-core r-base
```


> Install LinuxBrew. Linux brew is another package manager, but for scientific software. We will use it basically every week!

```
sudo mkdir /home/linuxbrew
sudo chown $USER:$USER /home/linuxbrew
git clone https://github.com/Linuxbrew/brew.git /home/linuxbrew/.linuxbrew
echo 'export PATH="/home/linuxbrew/.linuxbrew/bin:$PATH"' >> ~/.profile
echo 'export MANPATH="/home/linuxbrew/.linuxbrew/share/man:$MANPATH"' >> ~/.profile
echo 'export INFOPATH="/home/linuxbrew/.linuxbrew/share/info:$INFOPATH"' >> ~/.profile
source ~/.profile
brew tap homebrew/science
brew update
brew doctor
```

> Install gcc (a compiler) and hmmer (to make the phylogeny)

```
brew install gcc hmmer tmux
```


> You will download one of the 5 different datasets (use the same dataset). Do you remember how to use the `wget` and `gzip` commands from last week? Also, download Swissprot and Pfam-A

-


	cd /mnt

	#download your dataset

	https://www.dropbox.com/s/qqeneb6ajn3dt22/mystery.fasta

	#download the SwissProt database

	wget ftp://ftp.uniprot.org/pub/databases/uniprot/current_release/knowledgebase/complete/uniprot_sprot.fasta.gz

	#download the Pfam-A database

	wget ftp://ftp.ebi.ac.uk/pub/databases/Pfam/current_release/Pfam-A.hmm.gz


> we are going to run HMMER to identify conserved protein domains. This will take a little while, and we'll use `tmux` to allow us to do this in the background, and continue to work on other things.


    gzip -d *gz
    tmux new -s pfam
    hmmpress Pfam-A.hmm #this is analogous to 'makeblastdb'
    hmmscan -E 1e-3 --domtblout dataset.pfam --cpu 4 Pfam-A.hmm dataset1.fa
    ctl-b d
    top -c #see that hmmscan is running..


> the neat thing about HMMER is that it can be used as a replacement for blastP or PSI-blast.


    #blastp-like HBB-HUMAN is a Hemoglobin B protein sequence.

    phmmer --domtblout hbb.phmmer -E 1e-5 \
    /home/ubuntu/hmmer-3.1b1-linux-intel-x86_64/tutorial/HBB_HUMAN \
    uniprot_sprot.fasta

    #PSI-blast-like

    jackhmmer --domtblout hbb.jackhammer -E 1e-5 \
    /home/ubuntu/hmmer-3.1b1-linux-intel-x86_64/tutorial/HBB_HUMAN \
    uniprot_sprot.fasta

    #you can look at the results using `more hmm.phmmer` or `more hmm.jackhmmer`. Try blasting a few of the results using the BLAST web interface.


> Now let's look at the Pfam results. This analyses may still be running, but we can look at it while it's still in progress.


    more dataset.pfam
    #There are a bunch of columns in this table - what do they mean?

    #Try to extract all the hits to a specific domain. Google a few domains (column 1) to see if any seem interesting.

    #for instance, find all occurrences of ABC_tran
    grep ABC_tran dataset.pfam

    #use grep to count the number of matches. Copy this number down.

    grep -c ABC_tran dataset.pfam

    #Find all the contigs that have a ABC_tran domain.

    grep ABC_tran dataset.pfam | awk '{print $4}' | sort | uniq


> Just for fun, check on the Pfam search to see what it is doing...


    tmux attach -t pfam
    ctl-b d
