Lab 10: Transcriptome Assembly
--

See http://oyster-river-protocol.readthedocs.io

> Step 1: Launch an instance on Jetstream. For this exercise, we will use a m1.xlarge instance.

> Update, upgrade, install...

```
sudo apt-get update && sudo apt-get -y upgrade && sudo apt-get -y install ruby build-essential mcl python python-pip
```

>Install python modules

```
pip install cvxopt numpy biopython scipy
```

>Install LinuxBrew, then the following software..

```
brew install gcc python metis parallel
```

> Install the Oyster River Software

```
git clone https://github.com/macmanes-lab/Oyster_River_Protocol.git
cd Oyster_River_Protocol
make
cd 
```
## Make sure to put the things it says to, in `~/.profile`

> Download Illumina RNAseq data, and subsample it.

```
curl -LO https://s3.amazonaws.com/gen711/1.subsamp_1.fastq
curl -LO https://s3.amazonaws.com/gen711/1.subsamp_2.fastq
seqtk sample -s23 1.subsamp_1.fastq 100000 > reads.1.fq
seqtk sample -s23 1.subsamp_2.fastq 100000 > reads.2.fq
```

> Install BUSCO

```
### Download databases

mkdir Oyster_River_Protocol/busco_dbs && cd Oyster_River_Protocol/busco_dbs

# Eukaryota
curl -LO http://busco.ezlab.org/v2/datasets/eukaryota_odb9.tar.gz

tar -zxf eukaryota_odb9.tar.gz
cd

### Move and edit config file (change everyplace it says `mmacmane` to your user name)

mv Oyster_River_Protocol/software/config.ini Oyster_River_Protocol/software/busco/config/config.ini
nano Oyster_River_Protocol/software/busco/config/config.ini
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
