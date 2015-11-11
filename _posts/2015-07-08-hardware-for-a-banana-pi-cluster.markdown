---
layout: post
title: "Hardware for a Banana Pi cluster"
author: Jörn
date:   2015-07-08 22:58:54
categories: cluster bananapi hardware lego psu
---

![A new purpose for old Lego bricks](/assets/w800_P_20150708_210959.jpg)

While I like the idea of screwing the Banana Pis to the wall, somewhat like [The Beast](https://resin.io/blog/what-would-you-do-with-a-120-raspberry-pi-cluster/), I simply wanted to build this thing quickly without having to go into the cellar, sawing and drilling holes. Lucky me had a box of old LEGO bricks, which are in general [the ultimate tool for rapid prototyping](https://brickify.it/). And they required only one quick trip to the cellar. I was able to build the Banana Pi cage in one evening, redesigning and attaching it to the other cages over the next two evenings.

![The current Banana Pi node 1](/assets/w800_P_20150708_211215.jpg)

With newer or updated versions of SoC boards coming out I had to choose which one would be the foundation of my project. Ultimately, the decision was driven by the idea of using ceph as the storage for owncloud. That quickly brought me to the Bananas because they have a SATA interface and GB ethernet port. Both directly connected to the A20 ARM Cortex-A7 dual-core CPU for ~30€. Neither can be found on the Raspberry Pis. The BPI-M1+ comes with Wifi, which I don't need and the BPI-PRO has no SATA -> meh. The [Cubieboard 3](http://docs.cubieboard.org/tutorials/cubietruck/start) uses the same A20 CPU but brings 2GB of RAM and WiFi for three times the cost of a [Banana Pi M1](http://www.banana-pi.com/eacp_view.asp?id=35). The [pcDuino3B](http://www.linksprite.com/?page_id=812) also looks interesting, same A20. However, I still don't need wifi and it is more expensive than the BPI-M1.

It would be great to have a SoC with dual NICs but the Banana Pi R1 router board just uses a switch connected to the internal A20 NIC, so no gain there. Three interfaces would be awesome because then the web tier could separate incoming network traffic from storage as well as database traffic. The two USB ports however are also directly connected to the A20, so there might be some more options with USB NICs here. Whether or not the CPU can handle that remains to be benchmarked.

They do need to boot off something and I don't want to waste precious RAM by booting via TFTP and storing the rootfs in RAM. So I ordered 4GB SD-cards for each of the boards.

![Some cheap gigabit switch](/assets/w800_P_20150708_211452.jpg)

When thinking about this cluster I did not have an array of hundreds of SoC boards in mind. To be honest, the dimensions of the initial version was determined by the old 8-port GB ethernet switch that I had lying around. That pretty quickly led to ordering the seven Banana Pi boards. It also gave the initial cost a nice boundary. My hope is that the router is so cheap that we can actually saturate the backplane with the seven Banana Pis. Another benchmark.

![DIY Rubber band HDD mount](/assets/w800_P_20150708_212026.jpg)

I am a little proud of having built a HDD mount with rubber band and LEGO. We should all build more things out of LEGO. But I am drifting off. A minimal ceph installation requires 3 OSD nodes to replicate and distribute the data, so I also ordered three SATA adapters that pull the power from the Banana Pi. In the future I plan to have at least ten OSD nodes and hope to see and benchmark the claimed sustained performance under load. I'll need to ask around for old HDDs because I am running out of them.

![5V 20A PSU](/assets/w800_P_20150708_211321.jpg)

If you do the math 7*2A for BPI = 14A, 3*2A for HDD = 6A makes a total of 20A. So I ordered seven micro usb cables and a 20A 5V PSU to power all but the switch. Boring ... nothing to benchmark here. Well maybe if I can read some power usage with lmsensors? Ideas welcome.

If you have any questions or feedback send me an [email](mailto:jfd+owncluster@butonic.de) or ping me on [twitter](https://twitter.com/butonic).

Next up: installing owncloud on a single node
