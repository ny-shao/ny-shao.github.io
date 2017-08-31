---
layout: post
title: PECAM1/CD31 annotation missed in hg19/GenCODE
date: '2015-02-27 20:08:36'
tags:
- bioinformatics
- annotation
---

Today I found a funny thing, PECAM1/CD31 entry was absent in GenCODE v19.

[PECAM1/CD31](http://en.wikipedia.org/wiki/CD31) is a key surface marker of cell. One of my colleague asked me to check the expression level in one RNA-seq dataset. But I found it is absent in the GenCODE annoation database in the last version based on hg19.

The further investigation indicated that CD31 was in a [patched gap (HG183_PATCH, GL383558)](http://grch37.ensembl.org/Homo_sapiens/Gene/Summary?g=ENSG00000261371;r=HG183_PATCH:62399863-62491136) of hg19 genome. So it's not a big surprise for its absence in GenCODE v19. However, it's ready in the newest [hg38 genome](http://uswest.ensembl.org/Homo_sapiens/Gene/Summary?db=core;g=ENSG00000261371;r=17:64319415-64413776;t=ENST00000563924;redirect=no) and [GenCode annotations](http://www.gencodegenes.org/releases/) (currently it's version 21).

Generally, bioinformaticians would use old genome refence and annotation database 2-3 years after the release of newest genome, for the reseasons of "historical heritage". But here I need to update the refence genome and annotation database and rerun the RNA-seq pipeline because of the imperfect old annotations.