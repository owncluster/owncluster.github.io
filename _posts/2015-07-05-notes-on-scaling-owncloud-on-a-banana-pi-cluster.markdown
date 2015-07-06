---
layout: post
title:  "Notes on scaling ownCloud on a banana pi cluster"
author: "Jörn"
date:   2015-07-05 20:40:54
categories: ownCloud cluster bananapi
---

![Current Hardware](/assets/P_20150706_120053_010.jpg)

As an ownCloud developer I fairly often hear something like "ownCloud is awesome! But it's so slow on my Raspberry Pi". Let me tell you something: everything is slow on a rpi. ownCloud unfortunately will sooner or later be so convenient for most users that they try to upload their latest holiday pictures. All 3000 of them. While that will already take some time, the next time the gallery is opened the pi will come to a grinding halt because it starts generating thumbnails. A single raspberry pi / banana pi will run calendar end contacts just fine. Just dont expect it to handle file uploads equally well.

That being said, the [upcoming 8.1](https://github.com/owncloud/core/wiki/ownCloud-8.1-Features) has seen a lot of performance improvements. Time to finally get to the task and do some benchmarking. Benchmarking is hard ... and there are many aspects to consider when trying to benchmark owncloud. Furthermore, I really want to play with different deployment scenarios. Given the same hardware, what are the impacts of using mysql vs postgresql, redis vs memcached, apache vs nginx, ceph s3 vs phprados, does a second usb NIC make sense for ceph nodes based on a banana pi? Can I scale out ownCloud with banana pis? What will become the bottlenecks? When will the network become the bottleneck?

Answering all of these questions will take some time, so I created this blog to note down my findings. If you have any questions or feedback send me an [email](mailto:jfd+owncluster@butonic.de) or ping me on [twitter](https://twitter.com/butonic).

Next up: hardware details. Stay tuned!

