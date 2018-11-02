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
## Type

```
source ~/.profile
```

> Download Illumina RNAseq data, and subsample it.

```
cd
source activate orp_v2

curl -LO https://s3.amazonaws.com/gen711/1.subsamp_1.fastq
curl -LO https://s3.amazonaws.com/gen711/1.subsamp_2.fastq
seqtk sample -s23 1.subsamp_1.fastq 100000 > reads.1.fq
seqtk sample -s23 1.subsamp_2.fastq 100000 > reads.2.fq
```

> Edit config.ini

```

sed -i  "s_ubuntu_$(whoami)_g" $HOME/Oyster_River_Protocol/software/config.ini
```

> Assemble using the ORP

```
$HOME/Oyster_River_Protocol/oyster.mk main \
MEM=50 \
CPU=24 \
READ1=reads.1.fq \
READ2=reads.2.fq \
RUNOUT=smallassembly
 ```
# Terminate your instance
