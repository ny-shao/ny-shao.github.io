---
layout: post
title: Copy-Paste problem of Midnight Commander
date: '2015-04-24 19:14:22'
tags:
- linux
---

As you know, I am a big fan of midnight commander on terminal. But it still contains some wired problems as we know terminals of linux have a lot of bugs because of the historical problem. Here is one bug I frequently met: when you paste something to terminal, it always add characters like this: `your_strings` => `0~your_strings1~`. It's a [bug](https://bugs.launchpad.net/terminator/+bug/1350334) caused by [`bracketed paste mode`](http://cirw.in/blog/bracketed-paste) that is used to recognize the content users manually type or paste from anywhere else. And the solution is wired, in terminal type:
```
printf "\e[?2004l"
```
to turn off `bracketed paste mode`.