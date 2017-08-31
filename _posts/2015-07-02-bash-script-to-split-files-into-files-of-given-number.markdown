---
layout: post
title: BASH script to split files into files of given number
date: '2015-07-02 00:12:18'
tags:
- bash
- shell
- programming
---

Moved from my old blog.

Now I am coding for data processing with large reads files and for every line I use same function. So I need to split one file into given number files to use our 8 core server.

```shell
size=$(wc -l $1 | awk -F " " '{print $1}')
echo $size
splitlines=`expr $size / $2`
modulus=`expr $size % $2`
if [ $modulus -gt 0 ]
then
  splitlines=$((splitlines+1))
fi
split -d -l $splitlines $1 $3
```

The first prarameter is file name, the second is the number of files, and the third is the prefix of splited files.