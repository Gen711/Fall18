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


> Install the following software packages: `gcc samtools bedtools rna-star bcftools vcftools sratoolkit`


>Download data

```bash
curl -LO ftp://ftp.ensemblgenomes.org/pub/release-37/metazoa/fasta/anopheles_gambiae/dna/Anopheles_gambiae.AgamP4.dna_rm.toplevel.fa.gz
curl -LO ftp://ftp.ensemblgenomes.org/pub/release-37/metazoa/gtf/anopheles_gambiae/Anopheles_gambiae.AgamP4.37.chr.gtf.gz
prefetch SRR1727555
```

>Convert SRA files to fastQ format and un-compress other files.

```bash
fastq-dump --split-files --split-spot ncbi/public/sra/SRR1727555.sra
gzip -d Anopheles_gambiae.AgamP4.dna_rm.toplevel.fa.gz Anopheles_gambiae.AgamP4.37.chr.gtf.gz
```


> Index the genome

```bash
mkdir bad_mosquito

STAR --runMode genomeGenerate --genomeDir bad_mosquito \
--genomeFastaFiles Anopheles_gambiae.AgamP4.dna_rm.toplevel.fa \
--runThreadN 24 \
--genomeSAindexNbases 15 \
--sjdbGTFfile Anopheles_gambiae.AgamP4.37.chr.gtf
```

>Map reads!! (7 minutes). You're mapping an antenna transcriptome to the mosquito genome.

```bash
STAR --runMode alignReads \
--genomeDir bad_mosquito \
--readFilesIn SRR1727555_1.fastq SRR1727555_2.fastq \
--runThreadN 24 \
--outSAMtype BAM SortedByCoordinate \
--outFileNamePrefix squish
```

>Look at BAM file.


```bash
#Take a quick general look.

samtools index -@ 24 squishAligned.sortedByCoord.out.bam

samtools view -h -t Anopheles_gambiae.AgamP4.dna_rm.toplevel.fa --threads 24 squishAligned.sortedByCoord.out.bam | less -S

#use the spacebar to scan quickly

samtools tview squishAligned.sortedByCoord.out.bam Anopheles_gambiae.AgamP4.dna_rm.toplevel.fa
```

> Let's find SNPs, but just on the 2L chromosome.

```bash
samtools view -h -t Anopheles_gambiae.AgamP4.dna_rm.toplevel.fa --threads 12 squishAligned.sortedByCoord.out.bam \
| awk '$1 ~ "@" || $3=="2L"' \
| samtools view -h -t Anopheles_gambiae.AgamP4.dna_rm.toplevel.fa --threads 12 -1 -o 2L.bam -

samtools mpileup --skip-indels -A -u -t DP -f Anopheles_gambiae.AgamP4.dna_rm.toplevel.fa 2L.bam | \
    bcftools view -O v --threads 24 -v snps - > variants.vcf
```

> look at your vcf file. If we had mapped many individuals, we could calculate many interesting population genetics stats using the `vcftools` package.

```bash
less -S variants.vcf
```

# TERMINATE YOUR INSTANCE
