---
layout: post
title: Sort and generate the genome index from Gencode for aligners
date: '2014-10-17 21:46:39'
tags:
- ngs
- bioinformatics
---

Recently I need to generate genome index files for Ion Torrent results including ERCC, so I just used Gencode sequences and annotations.

Fasta files were downloaded [here](ftp://ftp.sanger.ac.uk/pub/gencode/Gencode_human/release_19/GRCh37.p13.genome.fa.gz), it is hg19 with patch version 13, and v19 is the last version of Gencode based on hg19. GTF annotation is [this](ftp://ftp.sanger.ac.uk/pub/gencode/Gencode_human/release_19/gencode.v19.annotation.gtf.gz).

ERCC sequences and annotation files were downloaded from [LifeTech](ftp://ftp.sanger.ac.uk/pub/gencode/Gencode_human/release_19/gencode.v19.annotation.gtf.gz).

[pyfasta](https://pypi.python.org/pypi/pyfasta/) was used to split the fasta file into small chromosome-based fasta files.

```
## Combine the sequences and annotations
cat GRCh37.p13.genome.fa ERCC92.fa > hg19_P13_ERCC.fa
cat gencode.v19.annotation.gtf ERCC92.gtf > hg19_P13_ERCC.gtf

## Split the fasta file into chromosome-based fasta files
## 1. for MapSplice
## 2. to modify the header of chromosomes and contigs
pyfasta split --header "%(seqid)s.fa" hg19_P13_ERCC.fa
rm hg19_P13_ERCC.fa

## Remove haplotype files we don't use
rm GL*.fa
rm JH*.fa
rm K*.fa

## Remove space and suffix in file name.
## Quotes were used in "${i}" because the files contain space.
for i in chr*.fa; do
	mv "${i}" $(echo $i | sed -e 's/ [0-9A-Z]*//g')
done

## Modify fasta header
for i in chr*.fa; do
	sed -i -e '1s/ [0-9A-Z]*//g' ${i} 
done

cat chr*.fa ERCC*.fa > hg19_P13_ERCC.fa
```

Now `hg19_P13_ERCC.fa` and `hg19_P13_ERCC.fa` are ready for the buiding of index.

```
## Build index for STAR
STAR --runMode genomeGenerate --genomeDir ./hg19_gencodev19_ERCC --genomeFastaFiles hg19_P13_ERCC.fa --runThreadN 8 -sjdbGTFfile hg19_P13_ERCC.gtf -sjdbOverhang 100

bowtie-build hg19_P13_ERCC.fa hg19_P13_ERCC
bowtie2-build hg19_P13_ERCC.fa hg19_P13_ERCC
```