---
layout: post
title: How to fix weird fonts when using Midnight Commander & PuTTY?
date: '2015-04-01 18:21:49'
tags:
- windows
- linux
---

I just borrow the titile of the post from [here](http://kb.gtkc.net/how-to-fix-weird-fonts-when-using-midnight-commander-putty/).

I found several posts on personal blogs or stackoverflow disscussed about it but they only show the solution is to set encode characterset to "UTF-8" or "iso885915" in PUTTY.

I checked the source. The reason is in `/etc/sysconfig/i18n`. For example, in one of our server, the content of `i18n`:

```
LANG="en_US.iso885915"
SYSFONT="lat0-sun16"
```

Then if the PUTTY was set to "UTF-8", the line characters were still wired fonts. You need to set the `$LANG` and PUTTY encode characterset in consistency. For this case, simply I set `$LANG` to "en_US.UTF-8" in `.bashrc` and the issue is gone.

```
## Set the $LANG for the line display of MC
export LANG="en_US.UTF-8"
```