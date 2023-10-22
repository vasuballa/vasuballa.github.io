---
layout: post
title: Oracle E-Business Suite - Virtual host names
description: Oracle E-Business Suite - Virtual host names
img: oracle-ebs-r12.png # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [ssl, oracleebs, virtualhosts, HA, DR]
comments: true
---

The ability to use virtual host names with Oracle E-Business Suite is one of the features that I have been waiting for a long time. When I finally saw a post on Steven Chan's [blog](https://blogs.oracle.com/ebstech/post/using-logical-hostnames-in-e-business-suite-release-12x) about it, I was very excited. But, when I finally got to review the Mos note "[Configuring Oracle E-Business Suite Release 12.x Using Logical Host Names](https://support.oracle.com/epmos/faces/DocumentDisplay?parent=DOCUMENT&sourceId=1968231.1&id=1968231.1)", I was left with disappointed. 

In my opinion, the main advantage of using virtual host names is during a DR failover scenario. By using virtual hosts we can setup the servers in both a primary datacenter and secondary datacenter to use the same virtual hostname, even though their physical hostnames are different. This virtual hostname setup helps when we failover services and databases to a secondary datacenter, as we don't have to reconfigure the application to use new physical hostnames. 

Currently when we install E-Business Suite to use a virtual hostname, "Concurrent Managers" dont work, as they internally use the physical hostname to communicate. The new MOS note describes this very feature of using virtual hostnames with Oracle E-Business Suite. But why I am disappointed? Because it left a very important use case out. In most cases when virtual hostnames are used, the servers are configured with a different physical hostname. i.e., if you run hostname or uname commands you will see that the actual physical hostname and virtual hostname is only present in DNS and hosts file. This scenario is not covered by the MOS note. 

The MOS note asks us to reconfigure the server with virtual hostname such that when we type hostname or uname command it shows the virtual hostname instead of the physical hostname. I believe the need to reconfigure the server to use a virtual hostname, defeats the main purpose of setting up virtual hostnames, making this MOS note useless :( Thus, I will keep on waiting for this out of the box feature. I currently have a custom in-house method to use virtual hostnames with E-Business Suite that I will blog about it in future.