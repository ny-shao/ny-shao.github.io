---
layout: post
title: How I setup working environment for my daily work
date: '2014-08-20 00:15:14'
tags:
- windows
- linux
- working
---

As a fulltime computational biologist, I spend almost all my time in front of my desktop to do my job. And as one of the earlist PhD students of [PICB](http://www.picb.ac.cn), I was always asked for advices from fresh students for the setting up of the working environment.

So here I have some assumptions about the conditions we face:

* One or several servers, and even cluster, but unfortunately, you don't have root privilege of them;
* One desktop, Linux or Window, with root privilege;
* A laptop, Linux or Windows, with root privilege.

In real world, what I have are:

* Some clusters, some servers, without root privilege;
* One Dell desktop, running win8.1, i7 CPU with 256G SSD;
* One Lenoveo X201i laptop, running win8.1, i3 CPU with 256G SSD.

## Applications work on both of Windows and Linux

* JabRef
* Dropbox
* Copy
* Atom and sublime text. For the editors, I use[Atom](https://atom.io/), an editor developped by [GitHub](http://www.github.com), with very good support to [Markdown](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet). Another favor of mine is [Sublime Text](http://www.sublimetext.com/3), but it is commercial.
* [R](http://www.r-project.org/) and [RStudio](http://www.rstudio.com/products/rstudio/download/).

And RStudio could be installed from the pre-built binary files downloaded from official websites.

## Windows desktop

There is always an issue to work in the windows environment as a bioinformatician, as Linux is dominant in the scientific computation, but generally people need windows as it is the dominant OS in desktop. I list my solutions here, but the final solution is a Linux VM or Linux Desktop.

* PuTTY is the mostly widely used SSH and Telnet implementation of Windows. I use [PuTTYTray](https://puttytray.goeswhere.com/), an improved version of PuTTy. 
* I don't like the original default display settings, it is ugly when you use ncureses application like `mc`, so [Tango Themes](https://madyogi.wordpress.com/tag/putty-tango-theme/) was used. To use it:
    * Import the `putty tango theme.reg` file into registery of windows
    * When set up a new PuTTY session, firstly load `tango theme`, then edit the settings of `Host Name`, `port` and things you want to set, change the name, and save the new named session.
* For the file tranferring, I use [WinSCP](http://winscp.net/eng/download.php), it is a client for FTP, SFTP, and with [Total commander](http://www.ghisler.com/download.htm)-like UI.
* Except Atom and Sublime Text, [notepad++](http://notepad-plus-plus.org/) is also good.

As the final solution, a Linux VM or Linux Desktop is necessary for a real bioinformatician. Both [Virtual Box](https://www.virtualbox.org/) and [VMPlayer](https://my.vmware.com/web/vmware/free#desktop_end_user_computing/vmware_player/) are free for non-commercial usage. I use VMPlayer as sometimes I could not install a 64bit Linux guest on a 64bit Win8 host.

## Linux VM or Desktop

Currently I run [Ubuntu 14.04 LTS](http://www.ubuntu.com/download/desktop) 64-bit on VMPlayer. Many softwares could be installed by PPA source of Ubuntu. To save time, we may just add repository first, and then just run `apt-get update` only once.

* [Midnight Commander](https://www.midnight-commander.org/) is my favorite file manager, a clone of Norton Commander. Under Ubutu, it could be installed from software center directly. In the menu (press`F9`), `Options` => `Panel options` => check `Lynx-like motion`, it will allow us to change the folder by arrow keys.
* To use PPA to install R in Ubuntu:
```shell
sudo add-apt-repository ppa:marutter/rrutter
sudo apt-get update
sudo apt-get install r-base r-base-dev
```
* To install Flash:
```shell
sudo add-apt-repository ppa:nilarimogard/webupd8
sudo apt-get update
sudo apt-get install freshplayerplugin
```
* To install Java of Oracle:
```
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update
sudo apt-get install oracle-java8-installer
sudo apt-get install oracle-java8-set-default
```
* I love [synapse launcher](https://launchpad.net/synapse-project), a launcher for Linux. to insall it:

```shell
sudo apt-add-repository ppa:synapse-core/testing
sudo apt-get update
sudo apt-get install synapse
```
* Atom as the editor. [Source(from webupd8)](http://www.webupd8.org/2014/05/atom-text-editor-ubuntu-ppa-update.html).
```shell
sudo add-apt-repository ppa:webupd8team/atom
sudo apt-get update
sudo apt-get install atom
```
* git.
* R and RStudio. And to install [Shiny server](http://www.rstudio.com/products/shiny/download-server/):
```shell
sudo su - \
-c "R -e \"install.packages('shiny', repos='http://cran.rstudio.com/')\""
sudo apt-get install gdebi-core
wget http://download3.rstudio.org/ubuntu-12.04/x86_64/shiny-server-1.2.1.362-amd64.deb
sudo gdebi shiny-server-1.2.1.362-amd64.deb
```
To save space of the `/home` folder (such as you use limited space SSD), we may setup R_LIBS at other folder (as remote file server). In such case, we need to do somethings for `~/.bash_profile` (for R console) and `~/.Renviron`.

In `~/.bash_profile`, we add:
```
export R_LIBS=/media/your_remote_fileserver/RLIBS
```
And in `~/.Renviron`, we add:
```
R_LIBS=/media/your_remote_fileserver/RLIBS
```
Now all R libraries will firstly installed at remote server, and save a lot space for your home folder!

## Connection between Linux desktop and Linux remote servers
* Generate SSH key of local desktop for remote connection.

```shell
> ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ny.shao/.ssh/id_rsa): 
Created directory '/home/ny.shao/.ssh'.
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/ny.shao/.ssh/id_rsa.
Your public key has been saved in /home/ny.shao/.ssh/id_rsa.pub.
The key fingerprint is:
xxxxxxxxxxxxxxxxxxxxx ny.shao@ubuntu
The key's randomart image is:
+--[ RSA 2048]----+
|                 |
|                 |
|                 |
|                 |
+-----------------+

```

Once your desktop has the key, you can just use it in the future, don't need to run it again.

* Copy your desktop key to remote servers.
```pretyprint lang-bash
ssh-copy-id user@remote-server.edu
```
If your remote server use different port, saying port `123456`:
```pretyprint lang-bash
ssh-copy-id -p 123456 user@remote-server.edu
```

* Touch a config file under `.ssh` of the local Linux desktop, and edit it.
```shell
cd ~/.ssh
touch config
```
Then open the config file with editor you prefer, add cotent like this:
```
Host *
  ServerAliveInterval 120

Host your_server_ssh
  ControlMaster auto
  ControlPath /tmp/ssh_mux_%h_p_%r
  HostName your.ssh.server.edu
  User user
  RemoteForward 52698 localhost:52698
  Port you_port
```
For the first section, I set a "heartbeat" for all ssh connections, and it will send a heartbeat every 120 seconds to keep the ssh connection alive.
In the next section, `user` is the user name, and `you_port` is the specific port for ssh if the server set. If default 22 port used, then line `Port` could be skipped. `RemoteForward` of port 52698 is set for `rmate` for the local `Atom` or `Sublime text` editor, you may modify it, but don't forget to change the port number in `rmate` then.

If the company or school has a "__gate__" node to login, you may follow this:
```
Host gate
  ControlMaster auto
  ControlPath /tmp/ssh_mux_%h_p_%r
  HostName gate.your_school.edu
  User user

Host your_sever_ssh
  ProxyCommand ssh -q gate nc -q0 server1.your_school.edu 22
  User user
  RemoteForward 52698 localhost:52698
```

Then if you want ssh login your server, you could:

```
ssh -XC your_server_ssh
```

* Install [remote-atom](https://atom.io/packages/remote-atom) package in Atom on local Linux desktop: `Ctrl+Shift+P` => `Install Packages`. For Sublime Text, package `rsub` need to be installed.

* Put `rmate` in some place in $PATH, generally I put it in `~/opt/app/rmate`.
```shell
wget --no-check-certificate -O ~/opt/app/rmate/rmate https://raw.github.com/aurora/rmate/master/rmate
chmod +x rmate
```
Later I will edit `.bashrc` to add paths under `~/opt/app` automatically.


* Now you may try:
```shell
rmate test.txt
```
Then you should find test.txt in your local atom window. If you get:
```
connect_to localhost port 52698: failed.
```
Then you need to start atome rmate server: in Atom, `Packages` => `Remote Atom` => `Start Server`.

## Linux server setup

* Setup at login. I prefer use `bash`, but `.bashrc` and `.bash_profile` are always an [issue](http://stackoverflow.com/questions/415403/whats-the-difference-between-bashrc-bash-profile-and-environment). To save time, I just put these at the end of my `.bash_profile`:
```shell
if [ -f ~/.bashrc ]; then
   source ~/.bashrc
fi
```

Then this snippet was added:
```shell
export HOME2="$HOME/opt"
if [ -d ${HOME2}/app ];
	then
	for j in $( ls ${HOME2}/app );
	do
	    if [ -d ${HOME2}/app/${j}/bin ];
	    then
		export PATH="${HOME2}/app/${j}/bin:$PATH"
	    else
		if [ -d ${HOME2}/app/${j} ];
		then
		    export PATH="${HOME2}/app/${j}:$PATH"
		fi
	    fi
	done
fi
```

So all subfolders under `~/opt/app` or `~/opt/app/*/bin` will be added to `$PATH`.

```shell
export R_LIBS=${HOME2}/Rpack
```
I also move my path of R packages to `~/opt/Rpack` to make it easy to be tracked.

* For the third party softwares, I prefer to use [pkgsrc](http://www.pkgsrc.org/) as a ready management system of software packages. It could help you install thousands softwares without root privilege, like `apt-get` in Debian/Ubuntu. Download the newest version, uncompress it, saying in the folder `~/opt/tmp/pkg_source_installation_date`, and run:

```shell
./bootstrap --prefix ~/opt/pkg_installation_date --unprivileged
```
  
  After it, add `~/opt/pkg_installation_date/bin` to your `$PATH`, that's it!
When you want to add some software, saying `tmux`, just go to `~/opt/tmp/pkg_source_installation_date/misc/tmux`, then:

```
bmake
bmake install
```

  The softwares I suggest to install are: `htop`, `tmux`, `midnight commander`, `eog`, `xpdf`, `emacs`. When you want to know if a software is in repo, just search it in [pkgsrc.se](http://pkgsrc.se/).

* Python. There are many way to solve python issue when you want to install a python package but you don't have root.
  * `pip install --user your_package`. But I don't recommend it.
  * `Virtualenv`. It is powerful, but I don't use it.
  * [`Enthought Canopy`](https://www.enthought.com/products/epd/) is a pre-built python bundle, could be installed under your home folder, and it is free for academic users. But I don't use it now.
  * [`Anaconda`](https://store.continuum.io/cshop/anaconda/). Another pre-built python bundle, and I am using it now.
  
  I use `Anaconda` or `Canopy` because they integerate many math packages, such as `numpy`, `scipy`, and `matplotlib`. If you want to install them manually, it will be tedious and trouble.
  
* Perl. I use [`Active Perl`](http://www.activestate.com/activeperl/downloads).

## Misc

* For R, some packages are so useful, I generally install them everywhere.
```r
install.packages(c("ggplot2", "reshape2", "plyr", "stringr", "sqldf", "data.table", "doMC", "caTools"), dep=TRUE)
source("http://bioconductor.org/biocLite.R")
biocLite("BiocInstaller")
biocLite(c("BSgenome", "Rsamtools", "ShortRead", "GenomicRanges", "DESeq2", "DEXSeq", "edgeR"))
```

* If you get error in installation of `RCurl` and `XML` in R:
In ubuntu:
```shell
sudo apt-get install libcurl4-openssl-dev
sudo apt-get install libxml2-dev
```
In CentOS:
```shell
sudo yum -y install curl
sudo yum -y install libcurl libcurl-devel
sudo yum -y install libxml2 libxml2-devel
```
[[Source]](http://stackoverflow.com/questions/20671814/non-zero-exit-status-r-3-0-1-xml-and-rcurl)

* Some reference:
  * For CRAN packages, please refer [this post](http://blog.yhathq.com/posts/10-R-packages-I-wish-I-knew-about-earlier.html).
  * For Next Generation Sequencing data analysis, please read [this guide](http://www.bioconductor.org/help/workflows/) on Bioconductor.
  * Somebody prefer to edit their `~/.Rprofile` to [customize there R environment](http://gettinggeneticsdone.blogspot.com.es/2013/07/customize-rprofile.html), like `options(stringsAsFactors=FALSE)` (I do hate this stupid default option!), but it is dangrous for the portability of your code.
  * Discussions about `Anaconda`, `Canopy`, and manually compiled python, could be found [here](http://stackoverflow.com/questions/15762943/anaconda-vs-epd-enthought-vs-manual-installation-of-python).