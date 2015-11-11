---
layout: post
title: Installing ownCloud on a Banana Pi
author: Jörn
date:   2015-07-27 17:14:54
categories: bananapi bananian install owncloud
excerpt_separator: ""
---

Before we can install ownCloud we have to get an OS installed first. How to download and copy the bananian image onto an SD card is described [over at the distribution](https://www.bananian.org/download#installation). Now lets see how you configure a few basics after logging in:

{% highlight shell-session %}
[jfd@lance]$ ssh bananian -l root
root@bananian's password: <the default password is "pi">
Linux bananian 3.4.104-bananian #1 SMP PREEMPT Mon Apr 6 18:25:40 UTC 2015 armv7l

------------------------------------------------------------------------ 
Welcome to Bananian Linux!
For news and updates check: https://www.bananian.org
Any questions? Read the FAQ first: https://www.bananian.org/faq

Run 'bananian-config' to set up Bananian Linux
Run 'bananian-update' to check for distribution updates
------------------------------------------------------------------------
root@bananian ~ # bananian-config

---------------------------------------------------------------------------------
Welcome to bananian-config! 

This script assists you to set up some basic parameters... 

For news and updates check: http://www.bananian.org 

---------------------------------------------------------------------------------
No keyboard found. Skipping keyboard configuration.

---------------------------------------------------------------------------------
Do you want to change your root password? (y/N) y

Configuring root password... 

Enter new UNIX password: <choose whatever suits you>
Retype new UNIX password: <choose whatever suits you>
passwd: password updated successfully

---------------------------------------------------------------------------------
Your current timezone is 'Etc/UTC'. Do you want to change it? (y/N) <enter>
---------------------------------------------------------------------------------
Your current locale is 'en_US.UTF-8'. Do you want to change it? (y/N) <enter>
---------------------------------------------------------------------------------
Your current hostname is 'bananian'. Do you want to change it? (y/N) y

Configuring hostname... (restart required) 

Enter new hostname: bpicln02 <choose whatever suits you>

---------------------------------------------------------------------------------
Video acceleration is currently disabled. Do you want to enable it? (y/N) <enter>
---------------------------------------------------------------------------------
Your current hardware configuration is: BananaPi
Do you want to change it? (y/N) <enter>
---------------------------------------------------------------------------------
Do you want to expand the root file system (recommended)? (y/N) y

Expanding root file system... (ignore the warnings and reboot immediately) 


Command (m for help): 
Disk /dev/mmcblk0: 3965 MB, 3965190144 bytes
4 heads, 16 sectors/track, 121008 cylinders, total 7744512 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x9f732a23

        Device Boot      Start         End      Blocks   Id  System
/dev/mmcblk0p1            2048       43007       20480   83  Linux
/dev/mmcblk0p2           43008     7744511     3850752   83  Linux

Command (m for help): Partition number (1-4): 
Command (m for help): Partition type:
   p   primary (1 primary, 0 extended, 3 free)
   e   extended
Select (default p): Partition number (1-4, default 2): First sector (43008-7744511, default 43008): Last sector, +sectors or +size{K,M,G} (43008-7744511, default 7744511): Using default value 7744511

Command (m for help): 
Disk /dev/mmcblk0: 3965 MB, 3965190144 bytes
4 heads, 16 sectors/track, 121008 cylinders, total 7744512 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x9f732a23

        Device Boot      Start         End      Blocks   Id  System
/dev/mmcblk0p1            2048       43007       20480   83  Linux
/dev/mmcblk0p2           43008     7744511     3850752   83  Linux

Command (m for help): The partition table has been altered!

Calling ioctl() to re-read partition table.

WARNING: Re-reading the partition table failed with error 16: Device or resource busy.
The kernel still uses the old table. The new table will be used at
the next reboot or after you run partprobe(8) or kpartx(8)
Syncing disks.
update-rc.d: using dependency based boot sequencing
update-rc.d: warning: default start runlevel arguments (2 3 4 5) do not match resize2fs_once Default-Start values (2 3 4 5 S)
update-rc.d: warning: default stop runlevel arguments (0 1 6) do not match resize2fs_once Default-Stop values (none)
Please reboot

---------------------------------------------------------------------------------
done! please reboot your system now! (shutdown -r now) 

root@bananian ~ # shutdown -r now

Broadcast message from root@bananian (pts/0) (Thu Jul 23 20:35:04 2015):

The system is going down for reboot NOW!
root@bananian ~ # Connection to bananian closed by remote host.
Connection to bananian closed.
[jfd@lance]$ ssh bpicln02 -l root
root@bpicln02's password: 
Linux bpicln02 3.4.104-bananian #1 SMP PREEMPT Mon Apr 6 18:25:40 UTC 2015 armv7l

------------------------------------------------------------------------ 
Welcome to Bananian Linux!
For news and updates check: https://www.bananian.org
Any questions? Read the FAQ first: https://www.bananian.org/faq

Run 'bananian-config' to set up Bananian Linux
Run 'bananian-update' to check for distribution updates
------------------------------------------------------------------------
Last login: Thu Jul 23 20:28:45 2015 from 192.168.1.105
root@bpicln02 ~ # 
{% endhighlight %}

That was not so hard, was it? Now how hard can it be to install ownCloud?

{% highlight shell-session %}
root@bpicln02 ~ # cat /etc/debian_version                       
7.8
root@bpicln02 ~ # wget http://download.opensuse.org/repositories/isv:ownCloud:community/Debian_7.0/Release.key
[...]
HTTP request sent, awaiting response... 200 OK
Length: 1003 [application/pgp-keys]
Saving to: `Release.key'

100%[===========================================================================================>] 1,003       --.-K/s   in 0s      

2015-07-30 19:54:58 (18.5 MB/s) - `Release.key' saved [1003/1003]

root@bpicln02 ~ # apt-key add - < Release.key  
OK
root@bpicln02 ~ # apt-get update                                                                              
[...]                                                                           
Reading package lists... Done
apt-get update  28.20s user 1.32s system 88% cpu 33.234 total
root@bpicln02 ~ # apt-get install owncloud ssl-cert mysql-server                                              
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following extra packages will be installed:
  apache2-mpm-prefork apache2-utils apache2.2-bin apache2.2-common fontconfig-config libaio1 libapache2-mod-php5 libapr1
  libaprutil1 libaprutil1-dbd-sqlite3 libaprutil1-ldap libclass-isa-perl libdbd-mysql-perl libdbi-perl libexpat1 libfontconfig1
  libfreetype6 libgd2-xpm libicu48 libjpeg8 libltdl7 libmagic1 libmcrypt4 libmysqlclient18 libonig2 libpcre3 libpng12-0 libpq5
  libqdbm14 libswitch-perl libx11-6 libx11-data libxau6 libxcb1 libxdmcp6 libxml2 libxpm4 lsof mime-support mysql-client-5.5
  mysql-common mysql-server-5.5 mysql-server-core-5.5 owncloud-3rdparty owncloud-app-activity owncloud-app-encryption
  owncloud-app-external owncloud-app-files owncloud-app-files-external owncloud-app-files-locking owncloud-app-files-pdfviewer
  owncloud-app-files-sharing owncloud-app-files-texteditor owncloud-app-files-trashbin owncloud-app-files-versions
  owncloud-app-files-videoviewer owncloud-app-firstrunwizard owncloud-app-gallery owncloud-app-provisioning-api
  owncloud-app-templateeditor owncloud-app-user-external owncloud-app-user-ldap owncloud-app-user-webdavauth owncloud-config-apache
  owncloud-server perl perl-modules php-pear php-xml-parser php5 php5-cli php5-common php5-curl php5-gd php5-intl php5-mcrypt
  php5-mysqlnd php5-pgsql php5-sqlite ttf-dejavu-core ucf
Suggested packages:
  www-browser apache2-doc apache2-suexec apache2-suexec-custom libgd-tools file libmcrypt-dev mcrypt libterm-readkey-perl tinyca
  clamav clamav-daemon smbclient libreoffice-writer php5-imagick libav-tools ffmpeg php5-ldap php5-apcu php-apc perl-doc
  libterm-readline-gnu-perl libterm-readline-perl-perl make libpod-plainer-perl php5-dev openssl-blacklist
Recommended packages:
  xml-core mailx libhtml-template-perl exim4 mail-transport-agent
The following NEW packages will be installed:
  apache2-mpm-prefork apache2-utils apache2.2-bin apache2.2-common fontconfig-config libaio1 libapache2-mod-php5 libapr1
  libaprutil1 libaprutil1-dbd-sqlite3 libaprutil1-ldap libclass-isa-perl libdbd-mysql-perl libdbi-perl libexpat1 libfontconfig1
  libfreetype6 libgd2-xpm libicu48 libjpeg8 libltdl7 libmagic1 libmcrypt4 libmysqlclient18 libonig2 libpcre3 libpng12-0 libpq5
  libqdbm14 libswitch-perl libx11-6 libx11-data libxau6 libxcb1 libxdmcp6 libxml2 libxpm4 lsof mime-support mysql-client-5.5
  mysql-common mysql-server mysql-server-5.5 mysql-server-core-5.5 owncloud owncloud-3rdparty owncloud-app-activity
  owncloud-app-encryption owncloud-app-external owncloud-app-files owncloud-app-files-external owncloud-app-files-locking
  owncloud-app-files-pdfviewer owncloud-app-files-sharing owncloud-app-files-texteditor owncloud-app-files-trashbin
  owncloud-app-files-versions owncloud-app-files-videoviewer owncloud-app-firstrunwizard owncloud-app-gallery
  owncloud-app-provisioning-api owncloud-app-templateeditor owncloud-app-user-external owncloud-app-user-ldap
  owncloud-app-user-webdavauth owncloud-config-apache owncloud-server perl perl-modules php-pear php-xml-parser php5 php5-cli
  php5-common php5-curl php5-gd php5-intl php5-mcrypt php5-mysqlnd php5-pgsql php5-sqlite ssl-cert ttf-dejavu-core ucf
0 upgraded, 84 newly installed, 0 to remove and 10 not upgraded.
Need to get 62.7 MB of archives.
After this operation, 248 MB of additional disk space will be used.
Do you want to continue [Y/n]? 
{% endhighlight %}

Wow that is a bunch of packages. 

