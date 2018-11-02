Lab 10: Transcriptome Assembly
--

See http://oyster-river-protocol.readthedocs.io

> Step 1: Launch an instance on Jetstream. For this exercise, we will use a m1.xlarge instance.

> Update, upgrade, install...

```
sudo apt-get update && sudo apt-get -y upgrade && sudo apt-get -y install build-essential git
```

> Install the Oyster River Software

```
git clone https://github.com/macmanes-lab/Oyster_River_Protocol.git
cd Oyster_River_Protocol
make
```
## Make sure to put the things it says to, in `~/.profile`, using `nano`. Paste them in at the bottom of the file

```
source ~/.profile
```

> Download Illumina RNAseq data, and subsample it.

```
cd
curl -LO https://s3.amazonaws.com/gen711/1.subsamp_1.fastq
curl -LO https://s3.amazonaws.com/gen711/1.subsamp_2.fastq
seqtk sample -s23 1.subsamp_1.fastq 100000 > reads.1.fq
seqtk sample -s23 1.subsamp_2.fastq 100000 > reads.2.fq
```

> Specify locations to BUSCO databases

```
### Download databases

sed -i  's_ubuntu_$(whoami)_g' $HOME/Oyster_River_Protocol/software/config.ini
```

> Assemble using the ORP

```
source activate orp_v2

$HOME/Oyster_River_Protocol/oyster.mk main \
MEM=50 \
CPU=24 \
STRAND=RF \
READ1=reads.1.fq \
READ2=reads.2.fq \
RUNOUT=smallassembly
 ```
# Terminate your instance
