Exam 2
--

1. Launch a m1.xlarge instance.  _______ 2 points


2. Update your machine and install the basic software using ``apt-get`` as per the genome assembly lab. _________ 2 points



3. Install `Linuxbrew` and the software as per the genome assembly lab ___________________ 2 points.


4. Download some data _________ 2 points

```
ftp://ftp.sra.ebi.ac.uk/vol1/fastq/ERR987/ERR987695/ERR987695_1.fastq.gz
ftp://ftp.sra.ebi.ac.uk/vol1/fastq/ERR987/ERR987695/ERR987695_2.fastq.gz
```

.. and the reference assembly

```
ftp://ftp.ncbi.nlm.nih.gov/genomes/all/GCA/001/938/725/GCA_001938725.1_ASM193872v1/GCA_001938725.1_ASM193872v1_genomic.fna.gz
```

5. Assembly the Illumina data using the `pe` (paired-end) data you just downloaded, using SPAdes and 2 different kmers. Use kmers of length `55` and `85`. Hint, in the lab we used mate pair data, designated by `mp1-1` and `mp1-2`. You do need those parts of the command. Assemblies should take about 12 minutes. ___________________ 4 points

6. Using quast, evaluate the assembled genomes in `k55/scaffolds.fasta` and `k85/scaffolds.fasta` and the reference genome you downloaded above (about 11 minutes). Using the `report.txt` file, just like you did in the lab. Which assembly is better and why? Note, there might be more than 1 right answer, so the justification is very important. ___________________ 8 points

# Terminate your instance!!
