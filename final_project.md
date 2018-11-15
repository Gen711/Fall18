Project Tips
==

Here is a list of tips for your project.

0. Use tmux and suspend your instance when not in use. This will stop us from "paying" for the machine, but will save your work.

1. Likely everybody doing a transcriptome assembly will need to fix the headers in your fastQ files _before_ running in the ORP. This is how

```
sed -i -e 's_ __' -e 's_ _/1 _' ERR1016675_1.fastq
sed -i -e 's_ __' -e 's_ _/2 _' ERR1016675_2.fastq
```

2. Most people will not need to subsample their read datasets. However, if you have more than
20-40 milloion reads, you probably should. This is how to sample to 20 million reads:

```
seqtk sample -s2343 SRRNUMBER_1.fastq.gz 20000000 > reads.1.fq
seqtk sample -s2343 SRRNUMBER_2.fastq.gz 20000000 > reads.2.fq
```

3. Send me screenshots or text files of error messages. 
