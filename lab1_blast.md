Lab 2: BLAST
--

During this lab, we will acquaint ourselves with the the software package BLAST. Your objectives are:


1. Familiarize yourself with the software, how to execute it, how to visualize results.

2. Regarding your dataset, tell me how some of these genes are related to their homologous copies.


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

```
sudo apt-get -y install ruby build-essential python python-pip gdebi-core r-base
```


> Install LinuxBrew. Linux brew is another package manager, but for scientific software. We will use it basically every week!

```
cd
wget https://keybase.io/mpapis/key.asc
gpg --import key.asc
\curl -sSL https://get.rvm.io | bash -s stable --ruby


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

> Install gcc (a compiler) and BLAST

```
brew install gcc blast
```

> To get a feel for the different options, type `blastp -help`. Which type of blast does this correspond to? Look at the help info for blastp and tblastx. There are A LOT of options, most of which you will be able to safely ignore. One of the challenges of bioinformatics is knowing about the options and which ones you can safely ignore, and which ones are important.


## Challenge...

>You have just returned from South America, where you captured a rare - never been seen before - desert mouse. You are interested in knowing how it survives, and start by trying to identify the Sodium transport genes, in particular a gene called Scn5a (https://en.wikipedia.org/wiki/Nav1.5). You've just had the animals' transcriptome sequenced, and are about to begin the search!! You *could* use web blast like you've done in the past, but there are 20166 new sequences that you need to search and that would take a loooooooong time.

>Download the data that you just generated, using this command:

```
curl -LO https://s3.amazonaws.com/macmanes_share/transcripts.fasta
```

>You know that the model organism, the lab mouse, has an excellent genome, and is probably closely related to the mystery mouse, You decide to use it to help identify the sodium transport genes in the new animal. Download the data:

```
curl -LO ftp://ftp.ensembl.org/pub/release-85/fasta/mus_musculus/cdna/Mus_musculus.GRCm38.cdna.all.fa.gz
gzip -d Mus_musculus.GRCm38.cdna.all.fa.gz
```

#### OK, let's do some blasting..

>1st step is to make the blast database

```
makeblastdb -in Mus_musculus.GRCm38.cdna.all.fa -out mus -dbtype nucl
```

>Now blast.. your results should be saved in a file called `blast.out`

```
blastn -db mus -max_target_seqs 1 -query transcripts.fasta \
-outfmt '6 qseqid qlen length pident gaps evalue stitle' -evalue 1e-10 -num_threads 6 -out blast.out
```


>look at `blast.out`. the 1st column is the ID of the desert mouse transcript, the 2nd column is the e-value (a statistic related to how good the match between query and reference sequences is - smaller numbers better. The 3rd column is the Mouse transcript match descriptor)

```
less -S blast.out
```

>Oh crap.. There are 16501 lines in that file, how are we going to find the Scn5a gene that we are looking for?? Meet `grep`.

```
grep -i SCN5A blast.out
```

## Visualize blast results...

> Download and install RStudio

```
wget https://download2.rstudio.org/rstudio-server-1.0.143-amd64.deb
sudo gdebi -n rstudio-server-1.0.143-amd64.deb
```

> Find out the web address of your server

```
echo My RStudio Web server is running at: http://$(hostname):8787/
```

> Make a password (make is easy!!!)

```
sudo passwd username
```

>Note that the text will not echo to the screen (because it’s a password!)

>Return to the browser login page and enter your new password. Note this will not change the global XSEDE login info (i.e. it only affects this instance).

>Once R is up and running, we’ll give you a quick tour of the RStudio Web interface for those of you who haven’t seen it.


#### in RStudio (not the terminal)

>Install packages and read in the results file you just made.

```
install.packages(c("readr", "beanplot"))
library(c("readr", "beanplot"))

blast <- read_delim("~/blast.out", "\t",
    escape_double = FALSE, col_names = FALSE,
    trim_ws = TRUE)

View(blast)

```

> look at the results!!!

```
par(mfrow = c(2,2))
barplot(mean(blast$X3/blast$X2), main='A barplot', ylab="Percent Aligned")

boxplot(blast$X3/blast$X2, frame.plot=F, main='A boxplot', ylab="Percent Aligned")

beanplot(blast$X3/blast$X2, ll = 0, beanlinewd=-1, frame.plot=F, col="blue", main='A violin plot', ylab="Percent Aligned")

plot(blast$X3/blast$X2 ~ blast$X6, xlim=c(7e-11, 0), col='red',frame.plot=F, ylab="Percent Aligned", xlab='evalue', main="Evalue vs Percent Aligned")

```
