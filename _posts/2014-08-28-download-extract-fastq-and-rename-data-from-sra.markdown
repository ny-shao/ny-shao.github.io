---
layout: post
title: Download, extract fastq, and rename data from SRA
date: '2014-08-28 17:45:11'
tags:
- r
- sra
---

Recently I need to download, extract fastq, and rename data from public SRA repo. What I have is a manually-curated table of the name tags and ftp addresses of the files, like this:

```
H7-hESC_H3K27me3_0d_rep1	ftp://ftp-trace.ncbi.nlm.nih.gov/sra/sra-instant/reads/ByExp/sra/SRX%2FSRX189%2FSRX189966/SRR577347/SRR577347.sra
H7-hESC_H3K27me3_0d_rep2	ftp://ftp-trace.ncbi.nlm.nih.gov/sra/sra-instant/reads/ByExp/sra/SRX%2FSRX189%2FSRX189966/SRR577348/SRR577348.sra
```
The ideally outputs were the fastq files named as "H7-hESC\_H3K27me3\_0d\_rep1.fastq", and "H7-hESC\_H3K27me3\_0d_rep2.fastq".
So I wrote an R script to do this. My first idea is to use Bash, but finally I thought R is a good option. So here it is the R script:

```pretyprint lang-R
#! /usr/bin/env Rscript

# Read # col1 file name and #col2 SRA links to download and rename the fastq files.

getFq <- function(name.url){
  if (length(name.url[1])==0){
    return(NA)
  }
  file.name <- name.url[1]
  file.base <- basename(file.url)
  file.url <- name.url[2]
  SRR <- sub("^([^.]*).*", "\\1", file.base)
  SRR.fastq <- paste0(SRR, ".fastq")
  new.fastq.name <- paste0(file.name, ".fastq")
  download.file(file.url, file.base, method="wget")
  system(paste0("fastq-dump ", file.base))
  file.rename(SRR.fastq, new.fastq.name)
  system(paste0("tar cvzf ", new.fastq.name, ".tar.gz ", new.fastq.name))
}

file.matrix <- read.table("sample.txt", header=FALSE, stringsAsFactors=FALSE)
apply(file.matrix, 1, getFq)
```