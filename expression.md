======================
Lab 9: Gene Expression
======================

During this lab, we will acquaint ourselves with how to estimate gene expression. You will:



> Step 1: Launch an instance on Jetstream. For this exercise, we will use a _m1.large_ instance.



> Update and upgrade your computer like you have every other week. Yes, you may use notes and previous labs..


> Install Conda like you have every other week!


> Install the following software packages via Conda like you have every other week: `salmon kallisto sra-tools`

> Install bashplotlib

```
pip install bashplotlib
```


>Download data

```bash
cd
mkdir $HOME/data
cd $HOME/data
curl -LO http://datadryad.org/bitstream/handle/10255/dryad.72141/brain.final.fasta
curl -LO ftp://ftp-trace.ncbi.nlm.nih.gov/sra/sra-instant/reads/ByRun/sra/SRR/SRR157/SRR1575395/SRR1575395.sra
```

> Convert SRA format into fastQ (takes a few minutes)

```bash
cd $HOME/data
fastq-dump --split-files --split-spot SRR1575395.sra
```


> Use Kallisto to count

```bash
mkdir $HOME/quant
cd $HOME/quant
kallisto index -i transcripts.idx $HOME/data/brain.final.fasta
kallisto quant -t 10 -i transcripts.idx -o kallisto_output -b 100 $HOME/data/SRR1575395_1.fastq $HOME/data/SRR1575395_2.fastq
```


> Using Salmon to count


```bash
cd $HOME/quant
salmon index -t $HOME/data/brain.final.fasta -i transcripts_index --type quasi -k 31
salmon quant -p 10 -i transcripts_index --seqBias --gcBias -l a \
-1 $HOME/data/SRR1575395_1.fastq \
-2 $HOME/data/SRR1575395_2.fastq -o salmon_output
```

> look at estimates of TPM

```bash
cd $HOME/quant
cat salmon_output/quant.sf | sed -e 1,11d | sort -nk1 > salmon.quant
cat kallisto_output/abundance.tsv | sed -e 1d | sort -nk1 > kallisto.quant
paste salmon.quant kallisto.quant  | column -s $'\t' -t | awk '{print $1 "\t" ($3-$9)/((($3+$9)/2)+.001)}' > expression.diffs
```

> histogram

```bash
hist  -p '#' -c red <(cat expression.diffs)
```

=======================
TERMINATE YOUR INSTANCE
=======================
