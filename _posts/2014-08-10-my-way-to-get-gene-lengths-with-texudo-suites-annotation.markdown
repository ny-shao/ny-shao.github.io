---
layout: post
title: My way to get gene lengths with Texudo suites annotation
date: '2014-08-10 04:07:05'
tags:
- genome
- rna-seq
---

I worked on one RNA-seq dataset, and need to get the length of genes for the calculation of FPKM. So here are the codes I used to extract and merge the exons from Texudo suites annotations.

genGeneLength.sh:
```shell
#! /bin/bash

# get all exons
awk 'BEGIN{OFS="\t"}{if($3=="exon"){print $0}}' ~/data/cufflinks/Mus_musculus/Ensembl/NCBIM37/Annotation/Genes/genes.gtf > exon.gtf
# get coordinations
cut -f1,4,5,7 exon.gtf > exon_coord.txt
# get gene id
cut -f9 exon.gtf | cut -f3 -d";" | sed "s/gene_id \"//g" | sed "s/\"//g" > gene_id_exon.txt
sort gene_id_exon.txt | uniq > uniq_gene_id.txt
# get exons based on gene id
paste -d"\t" exon_coord.txt gene_id_exon.txt | awk 'BEGIN{OFS="\t"}{print $1, $2, $3, $5, $5, $4}' | \
	sort | uniq > gene_id_exon_coord.bed

# merge the overlapped exons based on gene id
# warning: very slow! need improvement
if [ -f "merged_genes.bed" ]; then
	rm merged_genes.bed
fi

cat uniq_gene_id.txt | while read line; do
	grep ${line} gene_id_exon_coord.bed | mergeBed -i - -s -nms | \
	awk -v line=${line} 'BEGIN{OFS="\t"}{print $1, $2, $3, line, line, $6}' >> merged_genes.bed
done

# generate final table
./exon2gene_length.R

# remove temp files
rm exon_coord.txt gene_id_exon.txt uniq_gene_id.txt gene_id_exon_coord.bed
```

exon2gene_length.R:

```r
#! /bin/env Rscript

x <- read.table("merged_genes.bed")
exon.len <- x[,3]-x[,2]
gene.exon.len <- data.frame(id=x[,4], exon.len=exon.len)
library(plyr)
gene.exon.len.total <- ddply(gene.exon.len, .(id), summarise, 
	gene.exon.len = sum(exon.len))
write.table(gene.exon.len.total, file="gene_exon_len_total.txt", 
	sep="\t", quote=FALSE, row.names=FALSE, col.names=FALSE)
```

And the final results in `merged_genes.bed` look like:
```
ENSMUSG00000000001	3253
ENSMUSG00000000003	895
ENSMUSG00000000028	2233
ENSMUSG00000000031	2368
ENSMUSG00000000037	6101
ENSMUSG00000000049	1584
ENSMUSG00000000056	4795
```