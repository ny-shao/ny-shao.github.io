---
layout: post
title: My tips about R
date: '2015-07-23 18:05:57'
tags:
- r
- programming
---

Here I want to keep some tips about R, most of them came from stackoverflow.

## Paste two column of strings

[Source](http://stackoverflow.com/questions/14568662/paste-multiple-columns-together-in-r)

```r
# your starting data..
data <- data.frame('a' = 1:3, 'b' = c('a','b','c'), 'c' = c('d', 'e', 'f'), 'd' = c('g', 'h', 'i')) 

# columns to paste together
cols <- c( 'b' , 'c' , 'd' )

# create a new column `x` with the three columns collapsed together
data$x <- apply( data[ , cols ] , 1 , paste , collapse = "-" )

# remove the unnecessary rows
data <- data[ , !( names( data ) %in% cols ) ]
```

## Sum of rows based on one column category variable

[Source](http://stackoverflow.com/questions/15047742/sum-of-rows-based-on-column-value)

```r
library(plyr)
ddply(df, "X1", numcolwise(sum))
```

## Splitting a dataframe string column into multiple different columns

[Source](http://stackoverflow.com/questions/18641951/splitting-a-dataframe-string-column-into-multiple-different-columns)

```r
text <- c("F.US.CLE.V13", "F.US.CA6.U13", "F.US.CA6.U13", "F.US.CA6.U13", 
"F.US.CA6.U13", "F.US.CA6.U13", "F.US.CA6.U13", "F.US.CA6.U13", 
"F.US.DL.U13", "F.US.DL.U13", "F.US.DL.U13", "F.US.DL.Z13", "F.US.DL.Z13"
)
x <- read.table(text = text, sep = ".", colClasses = "character")
head(x)

```

## Rename a single column name in a data.frame

[Source](http://stackoverflow.com/questions/7531868/how-to-rename-a-single-column-in-a-data-frame-in-r)

```r
# df = dataframe
# old.var.name = The name you don't like anymore
# new.var.name = The name you want to get

names(df)[names(df) == 'old.var.name'] <- 'new.var.name'
```

## Add name to row names column

[Source](http://stackoverflow.com/questions/17514648/how-do-i-name-the-row-names-column-in-r)

```r
# add the rownames as a proper column
myDF <- cbind(Row.Names = rownames(myDF), myDF)
myDF

#           Row.Names id val vr2
# row_one     row_one  A   1  23
# row_two     row_two  A   2  24
# row_three row_three  B   3  25
# row_four   row_four  C   4  26
```

## Unable to install packages in latest version of R

When I try to install a package, I got an error:

```
package ‘ggplot2’ is not available (for R version 3.2.5)
```

[Source](http://stackoverflow.com/questions/25599943/unable-to-install-packages-in-latest-version-of-rstudio-and-r-version-3-1-1)

```r
install.packages('package_name', dependencies=TRUE, repos='http://cran.rstudio.com/')
```
