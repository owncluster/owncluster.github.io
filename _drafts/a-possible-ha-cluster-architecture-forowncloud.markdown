---
title: A possible high availability cluster architecture for ownCloud
author: Jörn
---

todo diagram  of how I intend to deploy ownCloud on these seven "beasts". 

the general goal is to have no single point of failure in the deployment and use a scale out aproach for every service. forthermore, I wanr to run a single service on every machine, separating webserver from database from storage. unfortunately, that cannot be done with seven banana pis. i need three banana pis for the minimal ceph cluster. at leas ten nodes are recommended to see the performance benefits, althoug I doubt I will see them using banana pis wirh a single 1gb erhernet nic. i need two to deploy a mysql galera cluster (or anythin similar in postgres, maybe cider?) and finally two for apache. that leaves no machines for redis and the loadbalancers. for now i wil have to put them an rhe web tier machines as well.

actually, we are not domw yet. what about monitoring, ldap, dhcp ... oh so much fun :) I guess this might develop into a hobby...

next up: how to benchmark? send your thoughts to mail or twitter
