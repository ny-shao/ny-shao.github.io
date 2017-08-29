---
layout: post
title: About multiple factors analysis in DESeq2
date: '2014-08-10 04:21:58'
tags:
- genome
- rna-seq
---

# About multiple factors analysis in DESeq2

I found a thread about multiple factors analysis in DESeq2 in the mailing list of bioconductor is interesting. Most the content were contributed by reseacher named as Ming (a Chinese postdoc of NIH I think) and Michael Love (author of DESeq2). It is too long and I try to make them look neat.

[link](https://stat.ethz.ch/pipermail/bioconductor/2014-March/058204.html)

## Question

Hi, Mike and All:

I am testing DESeq2 for multi-factor contrast setting for my own data with more complex meta data but currently use the simpler dataset from the user guide for testing purpose, and run into some issues that need your input and advice.  Here are the commands (only show some more relevant outputs):


```r
library("DESeq2")

library("Biobase")
library("pasilla")
data("pasillaGenes")
countData <- counts(pasillaGenes)

> str(countData)
 int [1:14470, 1:7] 0 78 2 1 3187 369 0 4 0 942 ...
 - attr(*, "dimnames")=List of 2
  ..$ : chr [1:14470] "FBgn0000003" "FBgn0000008" "FBgn0000014" "FBgn0000015" ...
  ..$ : chr [1:7] "treated1fb" "treated2fb" "treated3fb" "untreated1fb" ...
> head(countData)
            treated1fb treated2fb treated3fb untreated1fb untreated2fb untreated3fb untreated4fb
FBgn0000003          0          0          1            0            0            0            0
FBgn0000008         78         46         43           47           89           53           27
FBgn0000014          2          0          0            0            0            1            0
FBgn0000015          1          0          1            0            1            1            2
FBgn0000017       3187       1672       1859         2445         4615         2063         1711
FBgn0000018        369        150        176          288          383          135          174

colData <- pData(pasillaGenes)[,c("condition","type")]

> str(colData)
'data.frame':	7 obs. of  2 variables:
 $ condition: Factor w/ 2 levels "treated","untreated": 1 1 1 2 2 2 2
 $ type     : Factor w/ 2 levels "paired-end","single-read": 2 1 1 2 2 1 1
> colData
             condition        type
treated1fb     treated single-read
treated2fb     treated  paired-end
treated3fb     treated  paired-end
untreated1fb untreated single-read
untreated2fb untreated single-read
untreated3fb untreated  paired-end
untreated4fb untreated  paired-end
> str(pData(pasillaGenes))
'data.frame':	7 obs. of  3 variables:
 $ sizeFactor: num  NA NA NA NA NA NA NA
 $ condition : Factor w/ 2 levels "treated","untreated": 1 1 1 2 2 2 2
 $ type      : Factor w/ 2 levels "paired-end","single-read": 2 1 1 2 2 1 1
> pData(pasillaGenes)
             sizeFactor condition        type
treated1fb           NA   treated single-read
treated2fb           NA   treated  paired-end
treated3fb           NA   treated  paired-end
untreated1fb         NA untreated single-read
untreated2fb         NA untreated single-read
untreated3fb         NA untreated  paired-end
untreated4fb         NA untreated  paired-end

colData<-data.frame(colData,paste(colData$condition,colData$type,sep="."))
colnames(colData)[3] <- "condition_type";
> dds <- DESeqDataSetFromMatrix(countData = countData,colData = colData, design = ~ condition_type)
> colData(dds)
DataFrame with 7 rows and 4 columns
             condition        type        condition_type sizeFactor
              <factor>    <factor>              <factor>  <numeric>
treated1fb     treated single-read   treated.single-read  1.5116926
treated2fb     treated  paired-end    treated.paired-end  0.7843521
treated3fb     treated  paired-end    treated.paired-end  0.8958321
untreated1fb untreated single-read untreated.single-read  1.0499961
untreated2fb untreated single-read untreated.single-read  1.6585559
untreated3fb untreated  paired-end  untreated.paired-end  0.7117763
untreated4fb untreated  paired-end  untreated.paired-end  0.783745

>dds <- DESeq(dds)

estimating size factors
estimating dispersions
gene-wise dispersion estimates
mean-dispersion relationship
final dispersion estimates
fitting generalized linear model
> resultsNames(dds)
[1] "Intercept"
[2] "condition_type_treated.single.read_vs_treated.paired.end"
[3] "condition_type_untreated.paired.end_vs_treated.paired.end"
[4] "condition_type_untreated.single.read_vs_treated.paired.end"

> colData(dds)
DataFrame with 7 rows and 4 columns
             condition        type        condition_type sizeFactor
              <factor>    <factor>              <factor>  <numeric>
treated1fb     treated single-read   treated.single-read  1.5116926
treated2fb     treated  paired-end    treated.paired-end  0.7843521
treated3fb     treated  paired-end    treated.paired-end  0.8958321
untreated1fb untreated single-read untreated.single-read  1.0499961
untreated2fb untreated single-read untreated.single-read  1.6585559
untreated3fb untreated  paired-end  untreated.paired-end  0.7117763
untreated4fb untreated  paired-end  untreated.paired-end  0.7837458
```

Then I tried  a slight diiffermry setting of the design:

```r
> dds <- DESeqDataSetFromMatrix(countData = countData,colData = colData, design = ~0+ condition_type)

> dds <- DESeq(dds)
estimating size factors
estimating dispersions
gene-wise dispersion estimates
mean-dispersion relationship
final dispersion estimates
fitting generalized linear model
580 rows did not converge in beta, labelled in mcols(object)$betaConv. Use larger maxit argument with nbinomWaldTest
> resultsNames(dds)
[1] "condition_typetreated.paired.end"
[2] "condition_type_treated.single.read_vs_treated.paired.end"
[3] "condition_type_untreated.paired.end_vs_treated.paired.end"
[4] "condition_type_untreated.single.read_vs_treated.paired.end"
> colData(dds)
DataFrame with 7 rows and 4 columns
             condition        type        condition_type sizeFactor
              <factor>    <factor>              <factor>  <numeric>
treated1fb     treated single-read   treated.single-read  1.5116926
treated2fb     treated  paired-end    treated.paired-end  0.7843521
treated3fb     treated  paired-end    treated.paired-end  0.8958321
untreated1fb untreated single-read untreated.single-read  1.0499961
untreated2fb untreated single-read untreated.single-read  1.6585559
untreated3fb untreated  paired-end  untreated.paired-end  0.7117763
untreated4fb untreated  paired-end  untreated.paired-end  0.7837458
```

Then supposedly, I can use results(dds, "condition_type_treated.single.read_vs_treated.paired.end") to get DEGs for each contrast shown in resultsNames(dds).

here are my questions:

1. I used design = ~0+ condition_type instead of design = ~ condition_type in 2nd case, try to skip the intercept so that I can easily get all possible contrasts,  but seem not working the way I want.

2. I tried to get all possible contrasts:  but besides the contrasts shown in resultsNames(dds) in both cases, the contrasts like  untreated.single.read vs treated.single.read, untreated.paired.end vs untreated.single.read not even exists in the resultsNames(dds). also I like the contrast generally like: treated (including both treated.single.read and treated.paired-end) vs untreated (including both untreated.single-read and untreated  paired-end). I know for this case, we can just to design = ~condition, but I wish to do this in the same roof of one single design model although I can do a separate design. In limma and edgeR, there is a function like: `con.matrix<-makeContrasts()` where one can set up any contrasts under the design at will. Is there anythign like that in DESeq2? I understand we can do  `design(dds) <- formula(~ condition_type)`, but no contrast setting can be made at will. Anything in DESeq2 can get around that?

3. Also for simple contrast, I understand one can use relevel(colData(dds)$condition,"control") kind of command to set base level, but for multiple-factors contrasts as I am after, I almost need some kind of makeContrasts() mechanism to set up contrasts at will or have to do that individually one by one, which obvioulsy would be tedious and also these contrasts won't be in a single model roof. Anything can get around like that as well? if question 2 is addressed, this one shall be no problem.



Thanks in advance for your help! Appreciated very much!



Best



Ming

ATRF/NCI-Frederick,

Maryland, USA.

## Email from Ming

Hi, Mike:



Thx for the info, indeed I did try the following before with  the following for the data in user guide:

```r
> dds <- DESeqDataSetFromMatrix(countData = countData,colData = colData, design = ~ condition+type)
> dds <- DESeq(dds)
> resultsNames(dds)
[1] "Intercept"                      "condition_untreated_vs_treated"
[3] "type_single.read_vs_paired.end"
```

As you can see, the contrast I can get here is overall untreated_vs_treated and overall single.read_vs_paired.end, there is no subtype contrast such as treated.single.read vs treated.paired-end etc.

I would love to discuss briefly what I need here. I have a dataset which has tumors and matched normal samples from many patients, and there are subtypes of the tumors, say RasOnly, RasP, RasPNot types of tumors, of course, corresponding matched would be also with subtypes of  RasOnly, RasP,RasP1Hit, RasPNot, and the metadata like this:

```
          Subject SampleName   Type  RasType          RasTum
T6745_01A 49_6745  T6745_01A  Tumor     RasP      RasP.Tumor
N6745_11A 49_6745  N6745_11A Normal     RasP     RasP.Normal
T6761_01A 49_6761  T6761_01A  Tumor  RasPNot   RasPNot.Tumor
N6761_11A 49_6761  N6761_11A Normal  RasPNot  RasPNot.Normal
T5930_01A 50_5930  T5930_01A  Tumor RasP1Hit  RasP1Hit.Tumor
N5930_11A 50_5930  N5930_11A Normal RasP1Hit RasP1Hit.Normal
T5932_01A 50_5932  T5932_01A  Tumor  RasOnly   RasOnly.Tumor
N5932_11A 50_5932  N5932_11A Normal  RasOnly  RasOnly.Normal
........
```


Here are the contrasts I am interested to get DEGs:

```
RasOnly.Tumor vs RasOnly.Normal

RasP.Tumor vs RasP.Normal

RasP.Tumor + RasP1Hit.Tumor vs RasP.Normal+RasP1Hit.Normal

RasPNot.Tumor vs RasPNot.Normal

RasP1Hit.Tumor vs RasP1Hit.Normal

RasP.Tumor vs RasPNot.Tumor

RasP.Tumor+RasP1Hit.Tumor vs RasPNot.Tumor
RasOnly.Tumor vs RasPNot.Tumor

RasOnly.Normal vs RasPNot.Normal
RasP.Normal vs RasPNot.Normal

RasPRasP1Hit.Normal vs RasPNot.Normal,
 Tumor vs Normal

```

The last item Tumor vs Normal certainly can easily use design = ~ type to deal with. But many of the contrasts listed above not easy unless use the RasTum of the metadata shown above. I did try to use design=~Type+RasType

here are the commands:

```r
> dds <- DESeqDataSetFromMatrix(countData = countD,colData = colD,design = ~Type+RasType);
> show(resultsNames(dds))
character(0)
> dds <- DESeq(dds);
> resultsNames(dds)
[1] "Intercept"                   "Type_Tumor_vs_Normal"
[3] "RasType_RasP_vs_RasOnly"     "RasType_RasP1Hit_vs_RasOnly"
[5] "RasType_RasPNot_vs_RasOnly"
```

as you can see, I can only derive overall subtype contrasts from this way but not something like RasP.Tumor vs RasOnly.Tumor, the overall subtype contrasts for example, RasType_RasP_vs_RasOnly, consider both tumor and normal of RasP compared with those of RasOnly, which is certainly not what we want here.

user guide section 3.2 did show

```r
resCtrst <- results(ddsCtrst, contrast=c("treatment","OHT","DPN"))

resCtrst <- results(ddsCtrst, contrast=c(0,0,0,0,-1,1))
```

So besides RasTum, if you have a better way using just design = ~Type+RasType, that would be great.

I did try the following:

```r
> dds <- DESeqDataSetFromMatrix(countData = countD,colData = colD,design = ~RasTum);
> dds <- DESeq(dds);
estimating size factors
estimating dispersions
gene-wise dispersion estimates
mean-dispersion relationship
final dispersion estimates
fitting generalized linear model
172 rows did not converge in beta, labelled in mcols(object)$betaConv. Use larger maxit argument with nbinomWaldTest
> resultsNames(dds)
[1] "Intercept"
[2] "RasTum_RasOnly.Tumor_vs_RasOnly.Normal"
[3] "RasTum_RasP.Normal_vs_RasOnly.Normal"
[4] "RasTum_RasP.Tumor_vs_RasOnly.Normal"
[5] "RasTum_RasP1Hit.Normal_vs_RasOnly.Normal"
[6] "RasTum_RasP1Hit.Tumor_vs_RasOnly.Normal"
[7] "RasTum_RasPNot.Normal_vs_RasOnly.Normal"
[8] "RasTum_RasPNot.Tumor_vs_RasOnly.Normal"
```

I did get many contrasts as I desired, but the contrasts RasTum_RasP.Tumor_vs_RasOnly.Normal does not make sense here to me, but it shown up there in the resultsNames(dds) .



based on section 3.2, I seem be able to derived more from the above contrasts:

say: for contrast RasP.Tumor vs RasP.Normal, I can do:

```r
resCtrst<-result(dds, contrast=c(0,0,-1,1,0,0,0,0);
```

But is there any better way to do the above contrasts listed above that I desire?

Thx again for your advice!
best
Ming

## Email from Michael

Hi Ming,

Exploratory data analysis might be a more fruitful approach here rather
than brute force combinatorics and testing.

Copying from Wolfgang's recommendation in a similar situation:

"my advice here would be to put less emphasis on the testing and move
straight to clustering, using one of the transformations described in the
DESeq2 vignette to bring the data to a 'well-behaved' (log-like) scale."

"To filter out the genes that vary not much, use the range (max-min) or IQR
and a subjective cutoff (e.g. retain the top 20% of genes), then use
standard clustering functions (e.g. pam from the cluster package), and
other exploratory data analyses (e.g. PCA) to see the types of behaviours."

You might also try constructing a heatmap, as shown in the vignette, using
a subset of genes which vary the most, and then explore the grouping of
samples in the hierarchical clustering on the columns. For ease of
visualization, this subset should probably be in the 100s.

Mike

## Email from Michael

hi Ming,

To follow up on the question about contrasts, the way to perform
comparisons like "RasOnly.Tumor vs RasOnly.Normal", would be a design:

```r
Type + RasType + Type:RasType
```

where:

```r
results(dds, contrast=c("Type","Tumor","Normal"))
```

tests for the general effect,

and then the results for the interactions -- which are present in
resultsNames(dds) and can be extracted using the 'name' argument to
results() -- tests for an effect in a specific RasType which is different
than the general effect.

Mike

## Email from Ming

Hi, Mike:

Thx for your advice again and I did try what you suggested as below:

```r
> dds <- DESeqDataSetFromMatrix(countData = countD,colData = colD,design = ~Type + RasType + Type:RasType);
> dds <- DESeq(dds);
estimating size factors
estimating dispersions
gene-wise dispersion estimates
mean-dispersion relationship
final dispersion estimates
fitting generalized linear model
105 rows did not converge in beta, labelled in mcols(object)$betaConv. Use larger maxit argument with nbinomWaldTest
> show(resultsNames(dds));
[1] "Intercept"                   "Type_Tumor_vs_Normal"
[3] "RasType_RasP_vs_RasOnly"     "RasType_RasP1Hit_vs_RasOnly"
[5] "RasType_RasPNot_vs_RasOnly"  "TypeTumor.RasTypeRasP"
[7] "TypeTumor.RasTypeRasP1Hit"   "TypeTumor.RasTypeRasPNot"
```

certainly we can use

```r
> res_RasP_vs_RasOnly <- results(dds,"RasType_RasP_vs_RasOnly")
> res_RasP_vs_RasOnly <- results(dds,name="RasType_RasP_vs_RasOnly")
> res_Tumor_vs_Normal<-results(dds,"Type_Tumor_vs_Normal")
> res_Tumor_vs_Normal<-results(dds,name="Type_Tumor_vs_Normal")
```

but I can not do the following with contrast as suggested in section 3.2:

```r
> res_Tumor_vs_Normal<-results(dds,contrast=c("Type","Tumor","Normal"))
Error in results(dds, contrast = c("Type", "Tumor", "Normal")) :
  unused argument (contrast = c("Type", "Tumor", "Normal"))
>  res_RasP_vs_RasOnly <- results(dds,contrast=c("RasType","RasP","RasOnly"))
Error in results(dds, contrast = c("RasType", "RasP", "RasOnly")) :
  unused argument (contrast = c("RasType", "RasP", "RasOnly"))
```

also I can not get contrast like Tumor.RasP_Tumor.RasPNot:

```r
> res_Tumor.RasP_Tumor.RasPNot<-results(dds,contrast=c(0,0,0,0,0,1,0,-1))
Error in results(dds, contrast = c(0, 0, 0, 0, 0, 1, 0, -1)) :
  unused argument (contrast = c(0, 0, 0, 0, 0, 1, 0, -1))
```

it seems the interaction terms in the design (design = ~Type + RasType + Type:RasType) changed the behavior of results()?

Any idea or advice?
Thanks again for your time and help!

Ming

## Email from Ming

Hi, Mike:

Thanks for the help. Now we have updated the R and bioconductor version as well as the DESeq2. Here is sessionInfo()

```
R version 3.0.3 (2014-03-06)
Platform: x86_64-unknown-linux-gnu (64-bit)

[1] DESeq2_1.2.10
```

All the function calls now seem working, But I still got some issues for the contrasts I desired;

```r
> dds <- DESeqDataSetFromMatrix(countData = countD,colData = colD,design = ~Type + RasType + Type:RasType);
Usage note: the following factors have 3 or more levels:

RasType

For DESeq2 versions < 1.3, if you plan on extracting results for
these factors, we recommend using betaPrior=FALSE as an argument
when calling DESeq().
...

> colData(dds)$RasType <- factor(colData(dds)$RasType,levels=c("RasOnly","RasP","RasP1Hit","RasPNot"));

> colData(dds)$Type <- factor(colData(dds)$Type,levels=c("Normal","Tumor"))
> dds<-DESeq(dds, betaPrior=FALSE);
estimating size factors
estimating dispersions
gene-wise dispersion estimates
mean-dispersion relationship
final dispersion estimates
fitting model and testing

> show(resultsNames(dds));
[1] "Intercept"                   "Type_Tumor_vs_Normal"
[3] "RasType_RasP_vs_RasOnly"     "RasType_RasP1Hit_vs_RasOnly"
[5] "RasType_RasPNot_vs_RasOnly"  "TypeTumor.RasTypeRasP"
[7] "TypeTumor.RasTypeRasP1Hit"   "TypeTumor.RasTypeRasPNot"


> res_RasP_vs_RasOnly <- results(dds,"RasType_RasP_vs_RasOnly")
> res_RasP_vs_RasOnly <- results(dds,name="RasType_RasP_vs_RasOnly")
> res_Tumor_vs_Normal<-results(dds,contrast=c("Type","Tumor","Normal"))
> res_RasP_vs_RasOnly <- results(dds,contrast=c("RasType","RasP","RasOnly"))
> res_Tumor.RasP_vs_Tumor.RasPNot<-results(dds,contrast=c(0,0,0,0,0,1,0,-1))
```

I can get above contrast results working and got the DEGs lists. However, although I can get contrast result Tumor.RasP_vs_Tumor.RasPNot using `contrast=c(0,0,0,0,0,1,0,-1)` as guide indicated, some of desired contrasts I am not sure how to get:

I have 4 levels of RasType:  "RasOnly","RasP","RasP1Hit","RasPNot", it seems that RasOnly might be in intercept, since I did not see it in show(resultsNames(dds)). And I am interested in contrast: Tumor.RasOnly_Tumor.RasPNot, how do I get the results for this contrast?

my best guess is if TypeTumor.RasTypeRasOnly is at intercept, I can potentially get using below:

```r
Res_Tumor.RasOnly_vs_Tumor.RasPNot <-results(dds, contrast=c(1,0,0,0,0,0,0,-1).
```

However, I like to get the results of contrast: RasOnly.Normal_vs_RasPNot.Normal just to check normal contrast as background, then I am stuck, since no TypeNormal.RasTypeRasP contrast terms etc shown up in show(resultsNames(dds)).

Any advice here?

Thanks so much!
Best
Ming

## Email from Michael

Hi Ming,

just FYI, here are mailing list threads addressing this note in version 1.2:

http://permalink.gmane.org/gmane.science.biology.informatics.conductor/51749

http://permalink.gmane.org/gmane.science.biology.informatics.conductor/52331

To generate the contrast in tumor group between RasOnly and RasPNot
you can consider what the specifications for these groups would be in
the model matrix, and then subtract them to obtain your contrast.

The model matrix columns are given by resultsNames, so:

```
1 "Intercept"
2 "Type_Tumor_vs_Normal"
3 "RasType_RasP_vs_RasOnly"
4 "RasType_RasP1Hit_vs_RasOnly"
5 "RasType_RasPNot_vs_RasOnly"
6 "TypeTumor.RasTypeRasP"
7 "TypeTumor.RasTypeRasP1Hit"
8 "TypeTumor.RasTypeRasPNot"
```

the groups you want to compare would be then specified by the
following model matrix rows:

```
tumor and RasOnly = 1,1,0,0,0,0,0,0
tumor and RasPNot = 1,1,0,0,1,0,0,1
```

subtracting the first line from the second line (because you asked for
"Tumor-RasOnly vs Tumor-RasPNot") gives:

```
contrast=c(0,0,0,0,-1,0,0,-1)
```

which can be supplied as a contrast to results()

You can follow such steps to produce your contrasts of interest.

Mike

## EMail from Ming

Hi, Mike:

Thx a lot for the input and advice, which seems a great idea. Appreciated very much!

I also come up a way using combined terms of Type and RasType, which seems rather easy to derive the desired contrasts:

```r
> targets<-data.frame(targets,paste(targets$RasType, targets$Type,sep="."));
> colnames(targets)[ncol(targets)]<-"RasTum";
...
> dds <- DESeqDataSetFromMatrix(countData = countD,colData = colD,design = ~RasTum);
>colData(dds)$RasTum<-relevel(colData(dds)$RasTum,"RasP1Hit.Normal")
> dds<-DESeq(dds, betaPrior=FALSE);
estimating size factors
estimating dispersions
gene-wise dispersion estimates
mean-dispersion relationship
final dispersion estimates
fitting model and testing
> show(resultsNames(dds));
[1] "Intercept"
[2] "RasTum_RasOnly.Normal_vs_RasP1Hit.Normal"
[3] "RasTum_RasOnly.Tumor_vs_RasP1Hit.Normal"
[4] "RasTum_RasP.Normal_vs_RasP1Hit.Normal"
[5] "RasTum_RasP.Tumor_vs_RasP1Hit.Normal"
[6] "RasTum_RasP1Hit.Tumor_vs_RasP1Hit.Normal"
[7] "RasTum_RasPNot.Normal_vs_RasP1Hit.Normal"
[8] "RasTum_RasPNot.Tumor_vs_RasP1Hit.Normal"
```

then for contrast:

```r
RasOnly.Tumor_vs_RasPNot.Tumor,
res <- results(dds, contrast=c(0,0,1,0,0,0,0,-1));
```

for contrast: RasOnly.Normal_vs_RasPNot.Normal

```r
res <- results(dds, contrast=c(0,1,0,0,0,0,-1,0));
```

I will check if these two ways would give the same results.  Thanks again and best

Ming

## Email from Michael

hi Ming,

Let's keep the discussion on list, as it might be relevant to others.

On Mon, Mar 10, 2014 at 11:55 PM, Ming Yi <yi02 at hotmail.com> wrote:

```
>
> I am not sure how tumor and RasPNot = 1,1,0,0,1,0,0,1, could you explain to
> me a bit?
>
```

Let's work with the GLM formula in the vignette (Section 4.1 GLM)

we have

$$E(K_ij) = mu_ij = s_j 2^( x_j. beta_i )$$


if we divide out size factors and take log2 we have:

$$log2( mu_ij / s_j ) = x_j. beta_i$$

$x_j.$ is the $j$-th row of the model $matrix X$, and $beta_i$ is the vector of coefficients (a log2-scale intercept and log2 fold changes) for gene i.

Your model has a beta vector for each gene with values:

```
1 "Intercept"
2 "Type_Tumor_vs_Normal"
3 "RasType_RasP_vs_RasOnly"
4 "RasType_RasP1Hit_vs_RasOnly"
5 "RasType_RasPNot_vs_RasOnly"
6 "TypeTumor.RasTypeRasP"
7 "TypeTumor.RasTypeRasP1Hit"
8 "TypeTumor.RasTypeRasPNot"
```

Where Normal is the base level of Type and RasOnly is the base level of RasType.

So a sample which is Normal & RasOnly is given by

$$log2( mu_ij / s_j) = beta_Intercept$$

And this sample will have

```
x_j. = [1 0 0 0 0 0 0 0]
```

A sample which is Tumor & RasPNot is given by:

```r
log2( mu_ij / s_j) = beta_Intercept + beta_Type_Tumor_vs_Normal +
     beta_RasType_RasPNot_vs_RasOnly + beta_TypeTumor.RasTypeRasPNot
```

This is because, all samples get an intercept, Tumor_vs_Normal is the log2 fold change of tumor samples over normal, RasPNot_vs_RasOnly is the log2 fold change of RasPNot samples over RasOnly samples, and TypeTumor.RasTypeRasPNot is the additional interaction term for samples which are both Tumor and RasPNot.

And so this sample will have

```
x_j. = [1 1 0 0 1 0 0 1]
```

While the classic interaction linear model can be reparametrized sometimes to have only group means, we recommend users to use the following setup, because we need the intercept for the default setting of DESeq2 which includes moderation of log2 fold changes, but not the intercept.

Note that in DESeq2 version >= 1.3, we implement expanded model matrices, in which case what were base levels in this analysis will also get a log2 fold change (i.e. there are no longer base levels). So then this will change the constrasts, but it can be worked out using the above steps.

Mike

## Email from Ming

Hi, Mike:

Thx so much for your very detailed information and advcie on how to set up the contrasts in DESeq2. And the method is brilliant and relatively easy as well.

Just to confirm with what I did, I listed some contrasts that I set up based on your way for your to confirm:

so for my model with a beta vector for each gene with values:

```
> 1 "Intercept"
> 2 "Type_Tumor_vs_Normal"
> 3 "RasType_RasP_vs_RasOnly"
> 4 "RasType_RasP1Hit_vs_RasOnly"
> 5 "RasType_RasPNot_vs_RasOnly"
> 6 "TypeTumor.RasTypeRasP"
> 7 "TypeTumor.RasTypeRasP1Hit"
> 8 "TypeTumor.RasTypeRasPNot"


# normal and RasOnly= 1,0,0,0,0,0,0,0
# tumor and RasOnly = 1,1,0,0,0,0,0,0
```

so contrast `RasOnly.Tumor_RasOnly.Normal= c(0,1,0,0,0,0,0,0)`;

similarly

```
# normal and RasP= 1,0,1,0,0,0,0,0
# tumor and RasP  = 1,1,1,0,0,1,0,0
```

so contrast `RasP.Tumor_RasP.Normal=c(0,1,0,0,0,1,0,0)`;



I did run DESeq2 on these contrasts set up as above and got exactly the same results in numbers of DEGs (up, down and NO change at padj<=0.05) as compared to the contrast setting I mentioned ealier using combined terms of Type and RasType (see my email on Tuesday (Mar 11) to you).  So It seems both contrasts setting ways work exactly the same way.



Now my question is what about overall tumor vs overall normal, my best guess is:

```
Normals=1,0,1,1,1,0,0,0

Tumors  =1,1,1,1,1,1,1,1
```

and so contrast `Tumor_vs_Normal=c(0,1,0,0,0,1,1,1)`.

Am I correct?

Also another question is: I found many genes with valid baseMean and log2FoldChange, but with NA in p-value and padj columns and I checked the read counts of the samples, it looks like for those genes with NA in padj, many of samples  (but not all samples) have 0 counts, is this the reason how DESeq2 deal with those genes?

So this brings my third question, do you prefer to filter those genes out (I did not see example of filtering genes out before subjected to DESeq2 analysis in the user guide) instead of give NA in padj, which sometime could cause trouble in downstream analysis? Since when I used edgeR, it gives valid p-values or adjusted p-values for those genes but not NA, although not significant ones.

Thanks a lot in advance for your valuable opinions and help! Appreciated very much!

Best

Ming

---

I think this thread is useful and I paste here as a memo.
