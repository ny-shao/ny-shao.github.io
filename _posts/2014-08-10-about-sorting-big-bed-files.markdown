---
layout: post
title: About sorting big BED files
date: '2014-08-10 04:14:57'
tags:
- genome
---

It is a frequencing real world problem about sorting a big BED file.

There are many ways to do it. And the solutions I prefer are `bedtools` and `GNU sort`.

[bedtools](https://github.com/arq5x/bedtools2/releases) provides [sortBed](http://bedtools.readthedocs.org/en/latest/content/tools/sort.html) command for the sorting.

Things like this:
{% highlight bash %}
sortBed -i A.bed > A_sorted.bed
{% endhighlight %}

But `sortBed` of `bedtools` needs big memory of the server, so I also somtimes use `GNU sort`. `sort` utility in [GNU coreutils](http://www.gnu.org/software/coreutils/) now supports parallel computing and large cache.

Because generally our Linux servers are old RHEL or somethings old, so I generally use [pkgsrc](https://www.pkgsrc.org/) to install the relatively new version, in `pkgsrc_source/sysutils/coreutils`. And so on the new command is named as `gsort` to distiguish with `sort` in system $PATH.

So a typical command is:

{% highlight bash %}
gsort --parallel=16 -S 20G -k1,1 -k2,2n -k6,6 A.bed > A_sorted.bed
{% endhighlight %}

Here `16` are the cores used in sorting, and 20G is the cache size. `gsort` will sort the chromosome names firstly then the start position, then the strands.
