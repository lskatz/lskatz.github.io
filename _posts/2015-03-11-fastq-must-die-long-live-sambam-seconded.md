---
title: 'FASTQ must die! Long live SAM/BAM! (seconded!)'
date: 2015-03-11T16:18:00.002-04:00
draft: false
url: /2015/03/fastq-must-die-long-live-sambam-seconded.html
---

Here is a short rant but it becomes proactive.  
  
For those of you who don't know me, I dislike having two fastq files per genome if they are paired end (PE).  So I always shuffle them with my custom script [run\_assembly\_shuffleReads.pl](https://github.com/lskatz/CG-Pipeline/blob/master/scripts/run_assembly_shuffleReads.pl).  
  
I was going through [Lyve-SET](https://github.com/lskatz/lyve-SET), and putting in [SNAP](http://snap.cs.berkeley.edu/), when it broke with the following error message  
  
The two FASTQ files are not the same size! Make sure they contain  
the same reads in the same order, without comments.  
DEVTEAM: Allowing run, but you need to run on only one thread because the file splitter  
Doesn't understand how to deal with files that don't match byte-for-byte.  
Loading index from directory... 0s.  5412686 bases, seed size 16  
ConfDif MaxHits MaxDist MaxSeed ConfAd  %Used   %Unique %Multi  %!Found %Error  Reads/s  
PairedFASTQReader: failed to read mate.  The FASTQ files may not match properly.  

  

I got so frustrated because the input files are indeed paired end (PE) and there is no way to disable checking for PE in SNAP as far as I know.  Or if they are truly broken pairs, then please describe the actual problem set.  Besides, I am already checking for PE before running SNAP oh why won't you let me disable your checking??  
  
Anyway that got me thinking about how, even though I have a [script to check for interleaved files](https://github.com/lskatz/CG-Pipeline/blob/master/scripts/run_assembly_isFastqPE.pl), it is slightly buggy because it has to check for different formats of deflines: Casava1.7, Casava1.8, ..., fastq-dump -I, fastq-dump, ... , and also other random edge cases.  There are too many random and even some nonstandard deflines.  Why have all that?  Why not have a standard reads format that can describe everything?  And then I thought of the SAM format.  SAM describes PE, standardizes quality scores, and so much more.  Why not just convert all fastq files to unaligned SAM?  
  
Looking online, I found this really forward-thinking rant by Peter Cock: [http://blastedbio.blogspot.com/2011/10/fastq-must-die-long-live-sambam.html](http://blastedbio.blogspot.com/2011/10/fastq-must-die-long-live-sambam.html).  He touches on a lot of things I've been thinking.  That the deflines are not standardized.  That the quality scores are not standardized (well, I guess they mostly are now).  But then there are other small things he touches on--the free text area is too messy; the FLAG field in the SAM format can actually hold a value on whether a read passes Q/C; some or most modern tools can read SAM anyway.  
  
So here is the proactive part of this post.  And maybe I can try to make my posts in a ranty/proactivey format from now on (we'll see).  How to convert a fastq file into a sam file!  
  
  

1.  Get Picard: [http://broadinstitute.github.io/picard/](http://broadinstitute.github.io/picard/)
2.  Convert!  For example:

1.  java -jar /apps/x86\_64/picard/picard-tools-1.96/FastqToSam.jar F1=SRR1610028\_1.fastq F2=SRR1610028\_2.fastq SAMPLE\_NAME=tmp OUTPUT=out.sam

4.  Do samtools magic

1.  samtools view -bS out.sam > out.bam
2.  samtools sort out.bam out.sorted
3.  samtools index out.sorted.bam

Ok so now out.sorted.bam contains perhaps the most perfect possible representation of your data: 1) standardized format; 2) a single file for a single run instead of forward/reverse files; 3) if you do it right, you can do one group within the sam file per run; 4) compressible and sortable according to the bam format; 5) standardized quality score offsets; and 6) probably more I'm not thinking about right now.

  

Thoughts?

_note_ after the fact: my SNAP problem went away after I upgrade to the newest version (oops)
