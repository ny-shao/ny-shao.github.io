---
layout: post
title: Querying phastCon score by PostgreSQL database
date: '2014-08-10 04:03:22'
tags:
- database
- evolution
---

This is [an old post in my old blog](http://galaxyserenity.appspot.com/?p=9001), and here I repost it as a test.



phastCon score is a way to investigate the conservation of genomes, and its range is from 0 to 1, from unconserved to highly conserved. And it is based on multiple alignments of genomes, so in the gap regions of alignments, there is no score at all, *NaN*.

When I downloaded the phastCon score from UCSC, there are positions and scores in them.

Previously, I used SQLite to setup the database of phastCon score to do the querying. However, phastCon score of all genome may overload the ability of SQLite, the setup is slow and the parallel queryings are often locked. So now I use postgreSQL to setup the database, because the installation of PostgreSQL doesn't need root accession~
 
```shell
# 1. initiate the database at current working directory
initdb -D ./
# 2. start the database engine
pg_ctl -D . -l run.log start
# 3. create database named hg18phast
createdb hg18phast
```
 
Then I used bash and python scripts to parse files of phastCon files to text and dump to PostgreSQL, then I setup index of the database. (list.txt is the file list of chromosomes).

**A_do.sh**:
 
```shell
chr_files=`cat list.txt`
for chr in $chr_files
do
    python -u 01_psql_phastcon_setup_copy_txt.py $chr
done
```

And the python script, **01\_psql\_phastcon\_setup\_copy\_txt.py**, is:
 
```python
#!/usr/bin/env python# encoding: utf-8
"""
01_psql_phastcon_setup.py
"""

import sys
import os
import string
import subprocess
import time

now_path = '/home/phast/'

def main():
    name = sys.argv[1]
    print name
    ISOTIMEFORMAT='%Y-%m-%d %X'
    print time.strftime( ISOTIMEFORMAT, time.localtime( time.time() ) )
   
    in_f = file('../' + name + '.pp')
    out_f = file(name + '.txt', 'w')
    while True:
        line = in_f.readline()
        if len(line) == 0:
            break;
        line = string.strip(line)
        lineL = string.split(line)
        if 'start=' in line:
            start = int(string.replace(lineL[2], 'start=',''))
            pos = start
        if ('#' not in line) and ('chrom=' not in line):
            print >>out_f, '\t'.join([str(pos), str(lineL[0])])
            pos += 1
    in_f.flush()
    in_f.close()

    chrom_sql = name + '.sql'
    out_f = file(chrom_sql, 'w')
    print >>out_f, "create table %s (pos integer, value numeric);"%name
    print >>out_f, "copy %s from '/home/phast/%s.txt';"%(name, name)
    print >>out_f, "create index idx_pos_%s on %s(pos);"%(name, name)
    out_f.close()
    returnCode = subprocess.call(['psql', '-dhg18phast', '-Uuser', '-f' + chrom_sql])
    print returnCode
    print time.strftime( ISOTIMEFORMAT, time.localtime( time.time() ) )

if __name__ == '__main__':
    main()
```

 
After running of several hours, the database setup is done. In fact, the python script converted the file into plain text file, then generated a sql file, and called the sql file. The text files were directly dumped to database. The sql file looks like:

```shell
create table chrM (pos integer, value numeric);
copy chrM from '/home/hg18/phast/chrM.txt';
create index idx_pos_chrM on chrM(pos);
```
 
And with the help of [RPostgreSQL pacakge](http://cran.r-project.org/web/packages/RPostgreSQL/), we can query database in R:

```r
>library(RPostgreSQL)
>drv<-dbDriver("PostgreSQL")
>con<-dbConnect(drv, dbname="hg18phast", user="user")
>rs<-dbSendQuery(con, "select * from chrM where pos>200 and pos<250;")
>result<-fetch(rs)

> head(result)
  pos value
1 201     0
2 202     0
3 203     0
4 204     0
5 205     0
6 206     0
```

And if we hope to get the average score, we may replace the SQL query to:

```shell
select avg(value) from chrM where pos<=2000 and pos>=150;
```