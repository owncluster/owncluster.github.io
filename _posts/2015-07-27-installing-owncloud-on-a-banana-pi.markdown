---
layout: post
title: Installing ownCloud on a Banana Pi
author: Jörn
date:   2015-11-11 22:06:54
categories: bananapi bananian install owncloud
excerpt_separator: ""
---

Long time no post ... well, life happened and such. Show must go on ...

Before we can install ownCloud we have to get an OS installed first. How to download and copy the bananian image onto an SD card is described [over at the distribution](https://www.bananian.org/download#installation). Now lets see how you configure a few basics after logging in:

{% highlight shell-session %}
[jfd@lance]$ ssh bananian -l root
root@bananian's password: <the default password is "pi">
Linux bananapi 3.4.108-bananian #2 SMP PREEMPT Thu Aug 13 06:08:25 UTC 2015 armv7l

------------------------------------------------------------------------ 
Welcome to Bananian Linux!
For news and updates check: https://www.bananian.org
Any questions? Read the FAQ first: https://www.bananian.org/faq

Run 'bananian-config' to set up Bananian Linux
Run 'bananian-update' to check for distribution updates
------------------------------------------------------------------------

root@bananapi ~ # bananian-config
---------------------------------------------------------------------------------
Welcome to bananian-config! 

This script assists you to set up some basic parameters... 

For news and updates check: http://www.bananian.org 

---------------------------------------------------------------------------------
No keyboard found. Skipping keyboard configuration.

---------------------------------------------------------------------------------
Your current root password is still 'pi'. You have to change it now!

Configuring root password... 

Enter new UNIX password: <choose whatever suits you>
Retype new UNIX password: <choose whatever suits you>
passwd: password updated successfully

---------------------------------------------------------------------------------
Your current timezone is 'Etc/UTC'. Do you want to change it? (y/N) y

Configuring timezone... 


Current default time zone: 'Europe/Berlin'
Local time is now:      Wed Nov 11 20:59:42 CET 2015.
Universal Time is now:  Wed Nov 11 19:59:42 UTC 2015.

---------------------------------------------------------------------------------
Your current locale is 'en_US.UTF-8'. Do you want to change it? (y/N) y

Configuring locales... 

Generating locales (this might take a while)...
  de_DE.UTF-8... done
  en_US.UTF-8... done
Generation complete.

---------------------------------------------------------------------------------
Your current hostname is 'bananapi'. Do you want to change it? (y/N) y

Configuring hostname... (restart required) 

Enter new hostname: bpicln04 <choose whatever suits you>

---------------------------------------------------------------------------------
Your current hardware configuration is: BananaPi
Do you want to change it? (y/N) <enter>
---------------------------------------------------------------------------------
Do you want to expand the root file system (recommended)? (y/N) y

Expanding root file system... (ignore the warnings and reboot immediately) 


Welcome to fdisk (util-linux 2.25.2).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.


Command (m for help): Disk /dev/mmcblk0: 3.7 GiB, 3965190144 bytes, 7744512 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x0ccea0b3

Device         Boot Start     End Sectors  Size Id Type
/dev/mmcblk0p1       2048   43007   40960   20M 83 Linux
/dev/mmcblk0p2      43008 3710936 3667929  1.8G 83 Linux


Command (m for help): Partition number (1,2, default 2): 
Partition 2 has been deleted.

Command (m for help): Partition type
   p   primary (1 primary, 0 extended, 3 free)
   e   extended (container for logical partitions)
Select (default p): Partition number (2-4, default 2): First sector (43008-7744511, default 43008): Last sector, +sectors or +size{K,M,G,T,P} (43008-7744511, default 7744511): 
Created a new partition 2 of type 'Linux' and of size 3.7 GiB.

Command (m for help): Disk /dev/mmcblk0: 3.7 GiB, 3965190144 bytes, 7744512 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x0ccea0b3

Device         Boot Start     End Sectors  Size Id Type
/dev/mmcblk0p1       2048   43007   40960   20M 83 Linux
/dev/mmcblk0p2      43008 7744511 7701504  3.7G 83 Linux


Command (m for help): The partition table has been altered.
Calling ioctl() to re-read partition table.
Re-reading the partition table failed.: Device or resource busy

The kernel still uses the old table. The new table will be used at the next reboot or after you run partprobe(8) or kpartx(8).

Please reboot

---------------------------------------------------------------------------------
done! please reboot your system now! (shutdown -r now) 

bananian-config  14.26s user 1.85s system 13% cpu 2:03.23 total
root@bananapi ~ # shutdown -r now

Broadcast message from root@bpicln04 (pts/0) (Wed Nov 11 21:06:49 2015):

The system is going down for reboot NOW!
root@bananapi ~ # Connection to bananapi closed by remote host.
Connection to bananapi closed.
[jfd@lance]$ ssh bpicln04 -l root
root@bpicln04's password: 
Linux bpicln04 3.4.108-bananian #2 SMP PREEMPT Thu Aug 13 06:08:25 UTC 2015 armv7l

------------------------------------------------------------------------ 
Welcome to Bananian Linux!
For news and updates check: https://www.bananian.org
Any questions? Read the FAQ first: https://www.bananian.org/faq

Run 'bananian-config' to set up Bananian Linux
Run 'bananian-update' to check for distribution updates
------------------------------------------------------------------------
Last login: Wed Nov 11 21:08:25 2015 from 192.168.1.244
root@bpicln04 ~ # 
{% endhighlight %}

That was not so hard, was it? Now how hard can it be to install ownCloud? This is going to be a command line only install. I will list all commands I used to install and configure owncloud. I tried to comment the steps but ping me on irc or twitter if you have any questions.

{% highlight shell-session %}
# let's see what version we are on
root@bpicln04 ~ # cat /etc/debian_version
8.1
# ok, get the latest upgrades
root@bpicln04 ~ # apt-get update && apt-get upgrade
# now some basic LAMP setup (remember the db password, we need it later)
root@bpicln04 ~ # apt-get install -y apache2 mariadb-server libapache2-mod-php5 \
php5-gd php5-json php5-mysql php5-curl php5-intl php5-mcrypt php5-imagick 
# we should now be able to see debian stuff at http://bpicln04
# but since http is not good enough let's switch to https
root@bpicln04 ~ # apt-get install ssl-cert && a2enmod ssl && a2ensite default-ssl && service apache2 reload
# great, we now can access https://octestshib (after accepting a ton of warnings ... )
# let us start to install a standard oc setup
root@bpicln04 ~ # wget https://download.owncloud.org/community/owncloud-8.2.0.tar.bz2
root@bpicln04 ~ # apt-get install bzip2
root@bpicln04 ~ # tar -xvjf owncloud-8.2.0.tar.bz2
root@bpicln04 ~ # mv owncloud /var/www/
# by default apache on debian serves from /var/www/html. let us change that to /var/www/owncloud
root@bpicln04 ~ # sed -i 's_DocumentRoot /var/www/html_DocumentRoot /var/www/owncloud_' /etc/apache2/sites-enabled/default-ssl.conf
# there are some more apache modules oc needs as documented in https://doc.owncloud.org/server/8.2/admin_manual/installation/source_installation.html#additional-apache-configurations
root@bpicln04 ~ # a2enmod rewrite headers env dir mime
root@bpicln04 ~ # service apache2 restart
# you could now install oc by going to https://octestshib but we will use the `occ` commant to prevent timeouts and typos, see https://doc.owncloud.org/server/8.2/admin_manual/installation/command_line_installation.html
root@bpicln04 ~ # cd /var/www/owncloud
# let us create a datadir outside of our web root
root@bpicln04 ~ # mkdir /mnt/data
root@bpicln04 ~ # chown www-data.www-data /mnt/data
# sudo is not installed, we will just use su and install with occ
root@bpicln04 ~ # su www-data -s /bin/bash -c 'php occ  maintenance:install --database "mysql" --database-name "owncloud"  --database-user "root" --database-pass "password" --admin-user "admin" --admin-pass "password" --data-dir "/mnt/data"'
ownCloud is not installed - only a limited number of commands are available
{"reqId":"ztIEC9R7VwES7AiC+yu4","remoteAddr":"","app":"PHP","message":"Undefined offset: 0 at \/var\/www\/owncloud\/lib\/private\/setup\/mysql.php#45","level":3,"time":"2015-11-11T20:44:02+00:00"}
ownCloud was successfully installed
{"reqId":"ztIEC9R7VwES7AiC+yu4","remoteAddr":"","app":"PHP","message":"chmod(): No such file or directory at \/var\/www\/owncloud\/lib\/private\/log\/owncloud.php#92","level":3,"time":"2015-11-11T20:44:23+00:00"}
# hm the warnings are annoying, but can be ignored
# opening https://bpicln04 gives us a dialog regarding the trusted_domains, so we will add that
# unfortunately the occ commant in 8.2 cannot yet update arrays in the config file correctly, so we have to fallback to sed:
root@bpicln04 ~ # sed -i "s_0 => 'localhost',_0 => 'localhost', 1 => 'bpicln04',_" config/config.php
# we might as well set up the cli interface properly:
root@bpicln04 ~ # su www-data -s /bin/bash -c 'php occ config:system:set overwrite.cli.url --value http://bpicln04'
# we can now login at https://bpicln04 but the admin page still gives us a few warnings
# the HSTS can easily be enabled by adding it to the apache vhost:
root@bpicln04 ~ # sed -i 's|</VirtualHost>|\t<IfModule mod_headers.c>\n\t\t\tHeader always set Strict-Transport-Security "max-age=15768000; includeSubDomains; preload"\n\t\t</IfModule>\n\t</VirtualHost>|' /etc/apache2/sites-enabled/default-ssl.conf
# for transactional filelocking we need to installing redis
root@bpicln04 ~ # apt-get install -y redis-server php5-redis
root@bpicln04 ~ # service apache2 restart
# configure it to use unix socket only
root@bpicln04 ~ # sed -i 's_port 6379_port 0_' /etc/redis/redis.conf
root@bpicln04 ~ # sed -i 's_# unixsocket /tmp/redis.sock_unixsocket /var/run/redis/redis.sock_' /etc/redis/redis.conf
root@bpicln04 ~ # sed -i 's_# unixsocketperm 700_unixsocketperm 0777_' /etc/redis/redis.conf
root@bpicln04 ~ # sed -i 's_# ULIMIT=65536_ULIMIT=65536_' /etc/redis/redis.conf
root@bpicln04 ~ # service redis-server restart
# now setup oc to use it ... requires sed because it uses an array
root@bpicln04 ~ # su www-data -s /bin/bash -c 'php occ config:system:set redis --value FIXME'
root@bpicln04 ~ # sed -i "s_'FIXME'_['host' => '/var/run/redis/redis.sock','port' => 0,'timeout' => 0.0]_" config/config.php
root@bpicln04 ~ # su www-data -s /bin/bash -c 'php occ config:system:set memcache.local --value "\OC\Memcache\Redis"'
root@bpicln04 ~ # su www-data -s /bin/bash -c 'php occ config:system:set memcache.locking --value "\OC\Memcache\Redis"'
root@bpicln04 ~ # su www-data -s /bin/bash -c 'php occ config:system:set filelocking.enabled --value true'
# awesome, all checks on the admin page pass!
{% endhighlight %}

Now ... this is an installation with most bells and whistles. We could also configure php to use redis as the session cache. But thats a task when we split up the services and try to scale out.

Looking at the above we are now torturing the small BananaPi with apache, mariadb, redis and the file storage at the same time. The sd card will be the bottleneck ... or is it?

Next up: benchmarking and small optimizations

