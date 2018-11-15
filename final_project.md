Project Tips
==

Here is a list of tips for your project.

0. Use tmux and suspend your instance when not in use. This will stop us from "paying" for the machine, but will save your work.

1. Likely everybody doing a transcriptome assembly will need to fix the headers in your fastQ files _before_ running in the ORP. This is how

```
sed -i 's_ _/1 _' file.1.fq
sed -i 's_ _/2 _' file.2.fq
```

2. Most people will not need to subsample their read datasets. However, if you have more than
20-40 milloion reads, you probably should. This is how to sample to 20 million reads:

```
seqtk sample -s2343 SRRNUMBER_1.fastq.gz 20000000 > reads.1.fq
seqtk sample -s2343 SRRNUMBER_2.fastq.gz 20000000 > reads.2.fq
```

3. Send me screenshots or text files of error messages.

4. Using tmux

```
# to make a new tmux window

tmux new -s gen711

# you can name your tmux window whataever you want, not just gen711

# to reattach to an existing tmux window, named gen711

tmux at -t gen711

```

5. Note that if you are using tmux, and you close/restart your computer, you will be disconnected from your instance. Its ok!! Just reconnect to your instance, then re-attach to your existing tmux window, and you will back to where you left off. 
