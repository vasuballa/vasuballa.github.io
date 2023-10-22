---
layout: post
title: Creating a custom Oracle EBS preinstall RPM for RHEL7
description: Creating a custom Oracle EBS preinstall RPM for RHEL7
img: orcl-redhat.png # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [redhat, oracle, oracleebs]
comments: true
---

Oracle, by default, ships preinstall RPMs only for Oracle Linux. Preinstall RPMs make the life of DBA very easy by taking care of installing full laundry lists of required prereq RPMs for Oracle E-Business Suite, as well as setting all required kernel and limits parameters. Even though Oracle Linux is derived from Redhat Linux, we cannot install those preinstall RPMs on Redhat Linux. Oracle-supplied preinstall RPMs have a prerequisite of using UEK kernel. 

UEK kernel is Oracle Linux-specific, Redhat does not ship that kernel. This makes the RPM fail on Redhat and it won't set any required kernel parameters, etc., on Redhat. So I have created a new RPM for Redhat that achieves the same result using the SRPMs from Oracle You can download the RPM from the link below: [Github repo](https://github.com/vasuballa/oracle-rhel-preinstall/tree/master/RPMS/x86_64) If you want to prepare the RPM yourself, here are the steps to follow: 

```
$ wget https://oss.oracle.com/ol7/SRPMS-updates/oracle-ebs-server-R12-preinstall-1.0-3.el7.src.rpm 
$ rpm2cpio oracle-ebs-server-R12-preinstall-1.0-3.el7.src.rpm | cpio -idmv 
$ mkdir -p ~/rpmbuild/SOURCES/ 
$ mkdir -p ~/rpmbuild/SPECS/ 
$ cp oracle-ebs-server-R12-preinstall-1.0.tar.gz ~/rpmbuild/SOURCES/ 
$ cp oracle-ebs-server-R12-preinstall.spec ~/rpmbuild/SPECS/ 
$ vi ~/rpmbuild/SPECS/oracle-ebs-server-R12-preinstall.spec 
:%s/kernel-uek/kernel/g 
:%s/3.el7/3.rhel7/g 
$ rpmbuild -ba ~/rpmbuild/SPECS/oracle-ebs-server-R12-preinstall.spec 
$ ls -ltr /home/oracle/rpmbuild/RPMS/x86_64/oracle-ebs-server-R12-preinstall-1.0-3.rhel7.x86_64.rpm 
```

Good luck installing Oracle on Redhat Linux. Please let me know how these worked for you.