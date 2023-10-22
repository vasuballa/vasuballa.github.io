---
layout: post
title: Virtual host names solution for Oracle E-Business Suite R12.2
description: Virtual host names solution for Oracle E-Business Suite R12.2
img: oracle-ebs-r12.png # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [ssl, oracleebs, virtualhosts, HA, DR]
comments: true
---

You have seen me rant about Oracle' provided virtual hostname solution before. If you have not, check it out [here](https://r12dba.com/oracle-ebs-virtual-hostnames). In summary, Oracle provided solution basically asks you to change the physical hostname of the server to the virtual one, which effects no just oracle ebs, but everything running on that server. In my earlier blog [post](https://r12dba.com/oracle-ebs-virtual-hostnames-solution), I outlined the steps to get virtual hostname working for 11i, R12.0 and R12.1 releases. 

Oracle EBS R12.2 is a special case, as it uses both 32bit and 64bit JDK. I am happy to tell you that I have a working virtualhostname solution that works with EBS R12.2. Thanks to Maris Elsins who figured this out. 

In brief, the solution is to compile both 32bit and 64bit version of the fakehostname library and copy them to /usr/lib and /usr/lib64 directories with same name libfakehostname.so.1. And then set LD_PRELOAD to just libfakehostname.so.1 with out the absolute path. This way both 32bit and 64bit libraries pick up appropriate library corresponding to their architecture. Here are the steps to implement the solution 

```
#download the source code 
[applmgr@apps ~]$ wget https://github.com/nonspecialist/fakehostname/archive/master.zip 
[applmgr@apps ~]$ unzip master.zip 
[applmgr@apps ~]$ cd fakehostname-master/ 

#compile 32bit library 
[applmgr@apps fakehostname-master]$ gcc -fPIC -m32 -shared -Wl,-soname,libfakehostname.so.1 -ldl -o libfakehostname.so.1 fakehostname.c 

#compile 64bit library 
[applmgr@apps fakehostname-master]$ gcc -fPIC -m64 -shared -Wl,-soname,libfakehostname64.so.1 -ldl -o libfakehostname64.so.1 fakehostname.c 
[applmgr@apps fakehostname-master]$ ls libfakehostname* 
libfakehostname64.so.1 libfakehostname.so.1 

#copy the 32bit library to /usr/lib 
[applmgr@apps fakehostname-master]$ sudo cp libfakehostname.so.1 /usr/lib 

#copy 64bit library to /usr/lib64 with same name as 32bit library 
[applmgr@apps fakehostname-master]$ sudo cp libfakehostname64.so.1 /usr/lib64/libfakehostname.so.1 

#set LD_PRELOAD to the library name. Do not use the absolute path 
[applmgr@apps ~]$ export LD_PRELOAD="libfakehostname.so.1" 

#test the fake hostname 
[applmgr@apps ~]$ hostname apps.example.com 
[applmgr@apps ~]$ export MYHOSTNAME=virtapps.example.com 
[applmgr@apps ~]$ hostname virtapps.example.com
```
Now all EBS services that run under applmgr user will use the virtual hostname