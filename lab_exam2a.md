Exam 2 - 2nd section
--

1. Launch a m1.xlarge instance.  _______ 2 points


2. Update your machine and install the basic software using ``apt-get`` as per the genome assembly lab. _________ 2 points



3. Install `Linuxbrew` and the software as per the genome assembly lab ___________________ 2 points.


4. Download some data _________ 2 points

```
ftp://ftp.sra.ebi.ac.uk/vol1/fastq/ERR217/004/ERR2172254/ERR2172254_1.fastq.gz
ftp://ftp.sra.ebi.ac.uk/vol1/fastq/ERR217/004/ERR2172254/ERR2172254_2.fastq.gz
```

.. and the reference assembly

```
ftp://ftp.ncbi.nlm.nih.gov/genomes/all/GCA/000/156/755/GCA_000156755.1_ASM15675v1/GCA_000156755.1_ASM15675v1_genomic.fna.gz
```

5. Assembly the Illumina data using the `pe` (paired-end) data you just downloaded, using SPAdes and 2 different kmers. Use kmers of length `79` and `95`. Hint, in the lab we used mate pair data, designated by `mp1-1` and `mp1-2`. You do NOT need those parts of the command. Assemblies should take about 5 minutes. ___________________ 4 points

6. Using quast, evaluate each of the the assembled genomes in `<<folder_name_from_spades1>>/scaffolds.fasta` and `<<folder_name_from_spades2>>/scaffolds.fasta` and the reference genome you downloaded above (about 4 minutes). Using the `report.txt` file, just like you did in the lab. Which assembly is better and why? Note, there might be more than 1 right answer, so the justification is very important. ___________________ 8 points

# Terminate your instance!!
