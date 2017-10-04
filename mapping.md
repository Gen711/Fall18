## Read Mapping


During this lab, we will acquaint ourselves with read mapping. You will:

1. Install software and download data

2. Use sra-toolkit to extract fastQ reads

3. Map reads to dataset

4. look at mapping quality


The STAR manuscript: https://www.ncbi.nlm.nih.gov/pubmed/23104886
The STAR manual: https://github.com/alexdobin/STAR/blob/master/doc/STARmanual.pdf



> Step 1: Launch an instance on Jetstream. For this exercise, we will use a _m1.xlarge_ instance.

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
sudo apt-get -y install ruby build-essential python python-pip
```


> Install LinuxBrew like you have every other week!


> Install the following software packages: `gcc bwa samtools aria2 bedtools rna-star`


>Download data

```bash
aria2c ftp://ftp.ensemblgenomes.org/pub/release-37/metazoa/fasta/anopheles_gambiae/dna/Anopheles_gambiae.AgamP4.dna.toplevel.fa.gz
aria2c ftp://ftp.ensemblgenomes.org/pub/release-37/metazoa/gtf/anopheles_gambiae/Anopheles_gambiae.AgamP4.37.chr.gtf.gz
prefetch SRR1727555
```

>Convert SRA files to fastQ format and un-compress other files.

```bash
fastq-dump --split-files --split-spot ncbi/public/sra/SRR1727555.sra
gzip -d Anopheles_gambiae.AgamP4.dna.toplevel.fa.gz Anopheles_gambiae.AgamP4.37.chr.gtf.gz
```

Map reads!! (17 minutes). You're mapping to a mouse brain transcriptome reference.

```bash
mkdir bad_mosquito
STAR --runMode genomeGenerate --genomeDir bad_mosquito \
--genomeFastaFiles Anopheles_gambiae.AgamP4.dna.toplevel.fa \
--runThreadN 24 \
--genomeSAindexNbases 15 \
--sjdbGTFfile Anopheles_gambiae.AgamP4.37.chr.gtf

STAR --runMode alignReads \
--genomeDir bad_mosquito \
--readFilesIn SRR1727555_1.fastq SRR1727555_2.fastq \
--runThreadN 24 --outFilterScoreMinOverLread 0 --outFilterMatchNminOverLread 0 --outFilterMatchNmin 0 --outFilterMismatchNmax 2 \
--outSAMtype BAM SortedByCoordinate \
--outFileNamePrefix squish
```

Look at BAM file. Can you see the columns that we talked about in class?


```bash
#Take a quick general look.

samtools view squishAligned.sortedByCoord.out.bam | head
samtools index squishAligned.sortedByCoord.out.bam
samtools tview squishAligned.sortedByCoord.out.bam Anopheles_gambiae.AgamP4.dna.toplevel.fa
```


look at mapping stats. Figure out what this means.

```bash
samtools flagstat squishAligned.sortedByCoord.out.bam
```

```bash
samtools mpileup -uD -f Anopheles_gambiae.AgamP4.dna.toplevel.fa squishAligned.sortedByCoord.out.bam | \
    bcftools view -bvcg - > variants.raw.bcf

bcftools view variants.raw.bcf > variants.vcf
```

# TERMINATE YOUR INSTANCE
