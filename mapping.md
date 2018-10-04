## Week 6: Read Mapping


During this lab, we will acquaint ourselves with read mapping. You will:

1. Install software and download data

2. Use sra-toolkit to extract fastQ reads

3. Map reads to dataset

4. look at mapping quality


The STAR manuscript: https://www.ncbi.nlm.nih.gov/pubmed/23104886
The STAR manual: https://github.com/alexdobin/STAR/blob/master/doc/STARmanual.pdf



> Step 1: Launch an instance on Jetstream. For this exercise, we will use a _m1.large_ instance.

```
ssh -i jetkey username@xxx.xxx.xxx.xxx
```

> Update and upgrade your computer like you have every other week. Yes, you may use notes and pervious labs..


> Install the following...
```
sudo apt-get -y install build-essential python python-pip
```


> Install Conda like you have every other week!


> Install the following software packages via Conda like you have every other week: `samtools bedtools star bcftools vcftools sra-tools`


>Download data

```bash
cd
curl -LO ftp://ftp.ensemblgenomes.org/pub/metazoa/release-40/fasta/anopheles_gambiae/dna/Anopheles_gambiae.AgamP4.dna_rm.toplevel.fa.gz
curl -LO ftp://ftp.ensemblgenomes.org/pub/metazoa/release-40/gff3/anopheles_gambiae/Anopheles_gambiae.AgamP4.40.gff3.gz
curl -LO ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR172/005/SRR1727555/SRR1727555_1.fastq.gz
curl -LO ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR172/005/SRR1727555/SRR1727555_2.fastq.gz
prefetch -vv --progress 2 SRR1727555
gzip -d *gz
```

> split fastQ readFilesIn
```
fastq-dump --split-files --split-spot ncbi/public/sra/SRR1727555.sra
```


> Index the genome

```bash
mkdir bad_mosquito

STAR --runMode genomeGenerate --genomeDir $HOME/bad_mosquito \
--genomeFastaFiles $HOME/Anopheles_gambiae.AgamP4.dna_rm.toplevel.fa \
--runThreadN 24
```

>Map reads!! (10 minutes). You're mapping RNA data, from a mosquito antenna to the mosquito genome.

```bash
STAR --runMode alignReads \
--genomeDir bad_mosquito/ \
--readFilesIn $HOME/SRR1727555_1.fastq $HOME/SRR1727555_2.fastq \
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
