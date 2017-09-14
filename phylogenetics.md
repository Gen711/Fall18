Lab 3: Make a phylogeny!!!
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
echo "https://cloud.r-project.org/bin/linux/ubuntu xenial/" | sudo tee -a /etc/apt/sources.list
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

> Install gcc (a compiler) and BLAST

```
brew install gcc mafft raxml R
```


>Download the data that you just generated, using this command:

```
curl -LO https://s3.amazonaws.com/gen711/dataset1.pep
```

>You know that the model organism, the lab mouse, has an excellent genome, and is probably closely related to the mystery mouse, You decide to use it to help identify the sodium transport genes in the new animal. Download the data:

```
curl -LO https://s3.amazonaws.com/gen711/uniprot.pep
grep -A1 'ENSPTRP00000032491\|ENSPTRP00000032494' dataset1.pep > query.pep
grep --no-group-separator -A1 'HXA2_HUMAN\|HXA2_BOVIN\|HXA2_PAPAN\|HXA3_HUMAN\|HXA3_MOUSE\|HXA3_BOVIN\|HXA9_HUMAN' uniprot.pep > results.pep
cat query.pep results.pep > for_alignment.pep
```



>Align the proteins using mafft


```
mafft --reorder --bl 80 --localpair --thread 6 for_alignment.pep > for.tree
```

> Make a phylogeny
```
raxmlHPC-PTHREADS -f a -m PROTCATBLOSUM62 -T 6 -x 34 -N 100 -n tree -s for.tree -p 35 -o sp|P31269|HXA9_HUMAN
```


## Visualize blast results...

> Download and install RStudio

```
curl -LO  https://download2.rstudio.org/rstudio-server-1.0.143-amd64.deb
sudo gdebi -n rstudio-server-1.0.143-amd64.deb
```

> Find out the web address of your server. Paste the web address that comes up on the terminal, in to your browser.

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
install.packages(c("ape", "ggplot2"))
source("http://bioconductor.org/biocLite.R")
biocLite(c("ggtree", "Biostrings")

library("ape")
library("Biostrings")
library("ggplot2")
library("ggtree")

nwk <- system.file("extdata", "RAxML_bipartitionsBranchLabels.tree", package="ggtree")
tree <- read.tree(nwk)
ggtree(tree)


```

> look at the results!!!

```
tree <- read.raxml("RAxML_bipartitionsBranchLabels.tree")
ggtree(tree) + geom_label(aes(label=bootstrap, fill=bootstrap)) + geom_tiplab() +
+     scale_fill_continuous(low='darkgreen', high='red') + ggplot2::xlim(0, 3)
```


This lab uses code from ANGUS2017: https://angus.readthedocs.io/en/2017/visualizing-blast-scores-with-RStudio.html
