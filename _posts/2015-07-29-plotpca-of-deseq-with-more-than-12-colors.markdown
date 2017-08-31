---
layout: post
title: plotPCA of DESeq with more than 12 colors
date: '2015-07-29 23:02:54'
tags:
- rna-seq
- r
- bioinformatics
---

I need to plot a PCA results with more than 12 colors. Here is the code:

```r
library(genefilter)
library(gplots)
library(RColorBrewer)
library(lattice)

plotPCA4 <- function (x, intgroup = "condition", ntop = 500, all.colors=22){
  rv <- rowVars(exprs(x))
  select <- order(rv, decreasing = TRUE)[seq_len(ntop)]
  pca <- prcomp(t(exprs(x)[select, ]))
  fac <- factor(apply(pData(x)[, intgroup, drop = FALSE], 1,
    paste, collapse = " : "))
  if (length(fac) >= 3){
    getPalette <- colorRampPalette(brewer.pal(9, "Paired"))
    colours <- getPalette(all.colors)
  }
  else{
    colours <- c("darkred", "darkblue")
  }
  xyplot(PC2 ~ PC1, groups = fac, data = as.data.frame(pca$x),
    pch = 16, cex = 2, aspect = "iso", col = colours, 
    main = draw.key(key = list(rect = list(col = colours),
    text = list(levels(fac)), rep = FALSE)))
}

# DESeq steps:
# ...

myplot <- plotPCA4(vsd, intgroup = "colData", all.colors=22)

names <- rownames(colData)
myplot <- update(myplot, panel = function(x, y, ...) {
               panel.xyplot(x, y, ...);
               ltext(x=x, y=y, labels=names, pos=1, offset=1, cex=0.8)
            })
print(myplot)
```

The figure will be plotted with 22 colors and labelled with sample names.

Thanks to this [blog](http://novyden.blogspot.com/2013/09/how-to-expand-color-palette-with-ggplot.html).