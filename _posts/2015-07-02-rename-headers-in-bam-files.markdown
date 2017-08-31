---
layout: post
title: Rename headers in BAM files
date: '2015-07-02 00:08:35'
tags:
- chip-seq
- samtools
- casava
---

Moved from my old blog.

Now I am processing a batch of ChIP-Seq datasets, and got the datasets in bam format, and the headers of the chromosomes are "chr*.fa", boring CASAVA suite.
So for the fast way to remove ".fa" characters, I wrote the bash script:
 
```shell
for FILE in `ls *.bam`; do
    samtools view -H $FILE > header.txt
    sed 's/.fa//g' header.txt > new_header.txt
    samtools reheader new_header.txt $FILE > reheader/${FILE}
done
```
"convert_header.sh"