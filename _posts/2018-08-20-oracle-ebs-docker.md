---
layout: post
title: Oracle EBS Vision Instance on docker
description: Oracle EBS Vision Instance on docker
img: orcl-docker.ong # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [docker, oracleebs, oracle, linux]
comments: true
---

Continuing my previous efforts in getting docker running on Oracle Linux in this [blog post](https://r12dba.com/docker-oracle-linux/), I worked on getting Oracle E-Business Suite Vision Instance on docker. This blog explains the process. 

Many are aware that Oracle nowadays ships Vision Instance as single node virtual machine through their e-delivery site. This virtual machine is packaged as OVA format, which can be deployed on Virtualbox, Oracle VM server and even VMWare or Hyper-V. Instead of installing Oracle EBS Vision instance from installation media onto docker, I chose to use this VM image provided by Oracle. This blog explains the process of converting this Oracle EBS Vision Instance VM image into a docker image and deploying it. 

Let's get started with downloading the Oracle EBS 12.2.7 Vision instance image from Oracle Software Delivery Cloud aka edelivery.oracle.com. Search for "Oracle VM Virtual Appliance for Oracle E-Business Suite 12.2.7" on that website and download all the zip files related to it. Follow the process described in the README file in the zip file, and extract/build the OVA file. This OVA image is close 70 gigs in size, and an extracted VM image takes close to 250 gigs, so make sure you have at least half a terabyte of disk space to play with. Extract this ova file with tar command and it will give a vmdk disk image of the whole VM. 

```[root@docker EBS1227]# tar -xvf 1227.ova 
-rw------- someone/someone 11601 2018-01-31 09:59 Oracle-E-Business-Suite-12.2.7_VISION_INSTALL.ovf 
-rw------- someone/someone 73656125952 2018-01-31 12:59 Oracle-E-Business-Suite-12.2.7_VISION_INSTALL-disk001.vmdk 
[root@docker EBS1227]# 
```
We need to first install guestfs tool pkg which helps in extracting the required files from this VMDK disk image. Please note this is a disk-intensive operation and it is time-consuming. We are basically extracting u01 directory from the vmdk disk image of EBS Vision Virtual Machine. 
```
# yum install libguestfs-tools.noarch 
# export LIBGUESTFS_BACKEND=direct 
# guestfish --ro -a Oracle-E-Business-Suite-12.2.7_VISION_INSTALL-disk001.vmdk 
><fs> run 
><fs> list-filesystems /dev/sda1: ext4 /dev/vg_ebs/lv_home: ext4 /dev/vg_ebs/lv_root: ext4 /dev/vg_ebs/lv_swap: swap 
><fs> mount /dev/vg_ebs/lv_root / 
><fs> mount /dev/vg_ebs/lv_home /u01 
><fs> df 
Filesystem 1K-blocks Used Available Use% Mounted on 
tmpfs 97440 212 97228 1% /run /dev 240276 0 240276 0% 
/dev shmfs 243596 0 243596 0% /dev/shm 
/dev/mapper/vg_ebs-lv_root 20531068 2583276 16881808 14% 
/sysroot /dev/mapper/vg_ebs-lv_home 484438504 277620368 182187048 61% 
/sysroot/u01 
><fs> tar-out /u01 - | gzip -1 >> EBS1227-U01.gz 
><fs> quit 
```
This EBS1227-U01.gz file now has all the Apps code and database files related to EBS Vision Instance. We now need to import this to a docker image. For this, we are going to use oraclelinux docker image provided by Oracle as base and add the EBS files on top of that. I wrote a couple of scripts that will help in speeding up this process and Iput them on GitHub. Now proceed to this [github URL](https://github.com/vasuballa/docker-ebs/archive/master.zip) and download the files to your docker server. 

Copy the EBS1227-U01.gz you extracted from vmdk file to the directory where you extracted master.zip from github and run build.sh script. 

```
[docker@docker git]$ sh -x build.sh 
```
Verify that image stored in the docker repo using the command below. 
```
[docker@docker git]$ docker images |grep 1227 
r12dba/ebs 1227 4bd2ca6f64f0 2 hours ago 283GB 
```
You can launch the image using the command below. It starts a docker container with hostname apps.example.com and also exposes web port and db port to outside users 
```
[docker@docker git]$ docker run -it -d -p 8000:8000 -p 1521:1521 --name ebsvis01 -h apps.example.com r12dba/ebs:1227 
```
You can now access EBS url using https://apps.example.com:8000 . But note to add host file entry in your laptop and access. Here are some commands to verify the docker container: 

```
# check the running containers 
[docker@docker git]$ docker ps 
CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES 
7f5e42199455 r12dba/ebs:1227 "/bin/bash" 4 minutes ago Up 13 seconds 0.0.0.0:1521->1521/tcp, 0.0.0.0:8000->8000/tcp, 7001-7002/tcp ebsvis01 
[docker@docker git]$ 
# shell access to container 
[root@docker ~]# docker exec -it ebsvis01 bash 
# restart a container 
[root@docker ~]# docker restart ebsvis01 
```
The next step for me would be to deploy this on Kubernetes and see how we can scale this ...