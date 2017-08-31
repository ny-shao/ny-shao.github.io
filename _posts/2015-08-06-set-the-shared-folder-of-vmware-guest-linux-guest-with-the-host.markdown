---
layout: post
title: Set the shared folder of VMWare Linux guest with the host
date: '2015-08-06 22:17:29'
tags:
- linux
- vmware
---

As I mentioned [before](http://www.ny-shao.name/how-i-setup-working-environment/), I use a VMWare guest Linux virtual machine for the daily working. So I set a shared folder between the host and guest OS. In guest Linux, the folder should be mounted at `/mnt/hgfs`. But with the upgrade of the kernels, the shared folder will be disabled.

The solution is a repo of VMWare tools patches, hosted at [GitHub](https://github.com/rasa/vmware-tools-patches). Thanks for the authors now the shared folder works fine.