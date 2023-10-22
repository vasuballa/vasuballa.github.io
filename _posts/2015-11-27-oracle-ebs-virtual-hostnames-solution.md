---
layout: post
title: Oracle E-business suite: Virtual host names solution
description: Oracle E-business suite: Virtual host names solution
img: oracle-ebs-r12.png # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [ssl, oracleebs, virtualhosts, HA, DR]
comments: true
---

This blog post is a continuation to an earlier post about my musings on Oracle EBS support for virtual host[names](https://r12dba.com/oracle-ebs-virtual-hostnames). 

Actually, most parts of Oracle E-Business Suite work with virtual host names with out any problem. The only component that doesn't work when using virtual host names are the Concurrent Managers. Concurrent Managers expect that the node name defined in the Concurrent Manager definition screen matches the host name FNDLIBR executable reads at the server level. Having the virtual host name as an alias in the hosts file in the server doesn't cut it for the FNDLIBR executable. FNDLIBR reads the host name of the server using the Unix system call. This behaviour of FNDLIBR can be hacked by overriding the Unix gethostname system call using LD_PRELOAD functionality. 

There is already a prebuilt program out there on github to achieve this functionality. It's called [fakehostname](https://github.com/nonspecialist/fakehostname). I have tested this and verifies that it works with Oracle 11i, R12.0 and R12.1 version without any problem. 

Here is a demo: 
```
$ hostname 
ebs 
$ export LD_PRELOAD=/home/oracle/fakehost/libfakehostname.so.1 
$ export MYHOSTNAME=ebsfakehost 
$ hostname 
ebsfakehost 
$ export MYHOSTNAME=newebshost 
$ hostname 
newebshost   
```

This utility helps in making concurrent managers thinking that it's running on the virtual host by overriding the gethostname system call. This method of getting EBS to work with virtual hostnames doesn't work any more with EBS R12.2. The reason for this EBS R12.2 is that it it's shipped in a mix of 32bit and 64bit components. Earlier releases of EBS like 11i, 12.0 and 12.1 are 32bit only, even though they run on 64bit platforms. We can get EBS R12.2 working by having both 32bit and 64bit versions of the fakehostname library in the LD_PRELOAD, but EBS borks too many warning messages about not being able to load 32bit/64bit libraries, which defeats the whole purpose of having a simple solution. I am working on another way of getting virtual host names working in EBS R12.2. I will post that in my next blog post. Stay tuned! 