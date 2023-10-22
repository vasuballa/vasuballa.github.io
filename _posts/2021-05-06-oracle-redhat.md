---
layout: post
title: Enable Oracle Flash Cache on Red Hat Linux
description: Enable Oracle Flash Cache on Red Hat Linux
img: orcl-redhat.png # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [redhat, oracle, flashcache]
comments: true
---

The Oracle Smart Flash Cache feature was originally introduced in the 11gR2 version of Oracle Database. It works as an extension of the buffer cache in System Global Area (SGA). With this feature, instead of removing least recently used (LRU) data buffers from the SGA buffer cache, Oracle database offloads those LRU buffers into the local flash cache on the database server.

The best candidates for the local flash cache are SSD/NVMe. These are directly attached to the server instead of attached via SAN/NAS devices. Being a low-latency local disk, this helps speed up read workloads significantly. This is especially true on cloud platforms, where you get charged a much higher price for disks with higher provisioned IOPS (input/output operations per second).

For example in AWS, each EBS (Elastic Block Store) storage volume of type gp2, io1 or io2 has different storage characteristics in terms of the IOPS they can offer. The higher the IOPS the higher the price. A terabyte of gp2 volume costs about $100 a month and provides 3000 IOPS. The IOPS of a gp2 volume is tied to the volume. If you need higher IOPS, you need to switch to io1. It costs almost three times as much as gp2 volume for similar IOPS, but gives you an option to scale to even higher IOPS at even higher cost.

Recently, AWS released gp3 volume which provides the characteristics of io1 volume at a cheaper rate comparable to gp2 volume. The point is, we can move some of the read IOPS away from these storage volumes to local NVMe disks using Oracle Smart Flash Cache. It will help reduce the need to go with higher IOPS volume types like io1/io2, which are quite expensive.

One limitation with Smart Flash Cache is that Oracle Database only enabled this feature on the Oracle Linux (OL) platform. Even though OL is derived from Red Hat Enterprise Linux (RHEL), this feature is disabled on RHEL and other RHEL-based Linux flavors like CentOS. You’ll receive the following error message if you try:

```
[oracle@cenos7-test ~]$ sqlplus / as sysdba

SQL*Plus: Release 19.0.0.0.0 - Production on Wed May 5 00:11:03 2021
Version 19.3.0.0.0

Copyright (c) 1982, 2019, Oracle. All rights reserved.

Connected to an idle instance.

SQL> startup
ORA-00439: feature not enabled: Server Flash Cache
SQL>
```

After further research to determine if it’s feasible to enable this feature on Red Hat Linux, I found this old blog post from 2013 that talked about enabling this feature on Red Hat. With newer versions of Linux and Oracle database, the rpms and rpm signature it refers to have changed. After s-tracing and going over the logs, I managed to get the same fix working again for Red Hat/Centos 7 and Oracle Database 19c. Follow the steps below to get it working again:

## rename rpm exe and create a wrapper around it trick oracle to think oraclelinux-release rpm is installed

```
[root@cenos7-test bin]# pwd
/bin
[root@cenos7-test bin]# mv rpm rpm.bin
[root@cenos7-test bin]# vi rpm
[root@cenos7-test bin]# cat rpm
#!/bin/bash
if [ "$3" = "oraclelinux-release" ]
then
    echo 72f97b74ec551f03
else
    exec /bin/rpm.bin "$*"
fi
[root@cenos7-test bin]# chmod +x rpm
```

## create below file as well

```
[root@cenos7-test bin]# vi /etc/oracle-release
Oracle Linux Server release 7.9
[root@cenos7-test bin]#
```

I’m planning to create a custom rpm called oraclelinux-release which mentions 72f97b74ec551f03 in the rpm info output. This would eliminate the need for the above manual fixes.

Please ask any questions or let me know what you think in the commments.
