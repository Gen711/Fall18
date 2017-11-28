Exam 2 practical
--

> There have been only 3 labs since Exam 1 (Lab 8,9,10), so there is no secret that the exam will come from one of these labs. If you run through each of them, and then the practice (below), you should be in very good shape.  

1. Launch a m1.xlarge instance.


2. Update your machine and install the basic software using ``apt-get``, like you did in the kmer-counting lab.


3. Install linuxbrew, and the same software we used in the kmer-counting lab


4. Download the files `https://s3.amazonaws.com/gen711/1.subsamp_2.fastq` and `https://s3.amazonaws.com/gen711/1.subsamp_1.fastq`

5. Trim at a threshold of `5`, and normalize using a threshold of `50`. Use the code from the kmer-counting lab to do this.

6. Write the kmer abundances for the top 2-most abundant kmers from the normalized and un-normalized datasets.


# Terminate your instance!!
