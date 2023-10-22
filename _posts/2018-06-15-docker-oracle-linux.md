---
layout: post
title: Setting up Docker on Oracle Linux
description: Setting up Docker on Oracle Linux
img: orcl-docker.ong # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [docker, k8s, oracle, linux]
comments: true
---

Over the last couple of years, I have been hearing about Docker at different conferences. It's a container technology that seems to have gained a lot of popularity. In recent months, I have started exploring how Docker can be used with my area of specialization, Oracle E-Business Suite. I will be sharing my experiences with a series of blog posts on how to get E-Business Suite running on Docker. 

In this blog post, let's look at how to get started with running Docker on an Oracle Linux 7.5 machine. This install was done on a fresh Oracle Linux 7.5 VM. First, we need to install required packages for Docker. Docker packages are available via Oracle Public Yum Server under ol7_addons. So we can use the Yum utility to install the Docker using below commands: 

```
[root@docker ~]# yum install -y yum-utils 
[root@docker ~]# yum-config-manager --enable ol7_addons 
[root@docker ~]# yum install -y docker-engine 
```

By default, Docker pkg created a user group called Docker during install. Any user who is a part of this OS group can run Docker commands to start/stop containers or to import images. Let's create a new user called dadmin to administer Docker and add it to Docker OS user group. 

```
[root@docker ~]# useradd dadmin -g docker 
[root@docker ~]# passwd dadmin 
```

Docker stores all of its files (images, containers etc) in /var/lib/docker directory. So it's recommended to create a separate mount point to hold this directory and configure Docker to use overlay2 storage driver. It's the best storage driver for the XFS filesystem that comes with Oracle Linux. By default, Docker uses an overlay driver which has filesystem inodes issues. 

```
[root@docker ~]# df -h /var/lib/docker/ 
Filesystem Size Used Avail Use% Mounted on 
/dev/sdb1 2.0T 1.1T 999G 52% /var/lib/docker 
[root@docker docker]# cat /etc/docker/daemon.json 
{ "storage-driver": "overlay2", "storage-opts": [ "overlay2.override_kernel_check=true" ] }
```
Let's enable Docker service and bring it up: 
```
[root@docker ~]# systemctl status docker 
docker.service - Docker Application Container Engine Loaded: loaded (/usr/lib/systemd/system/docker.service; disabled; vendor preset: disabled) Drop-In: /etc/systemd/system/docker.service.d
docker-sysconfig.conf Active: inactive (dead) Docs: https://docs.docker.com 
[root@docker ~]# systemctl enable docker 
Created symlink from /etc/systemd/system/multi-user.target.wants/docker.service to /usr/lib/systemd/system/docker.service. 
[root@docker ~]# 
[root@docker ~]# systemctl start docker 
[root@docker ~]# systemctl status docker
docker.service - Docker Application Container Engine Loaded: loaded (/usr/lib/systemd/system/docker.service; enabled; vendor preset: disabled) 
Drop-In: /etc/systemd/system/docker.service.d ??docker-sysconfig.conf 
Active: active (running) since Wed 2018-05-30 10:26:26 EDT; 8s ago 
Docs: https://docs.docker.com Main PID: 11481 (dockerd) Tasks: 17 Memory: 16.5M 
CGroup: /system.slice/docker.service ??11481 /usr/bin/dockerd --selinux-enabled ??11486 docker-containerd -l unix:///var/run/docker/libcontainerd/docker-containerd.sock --metrics-interval=0 --start-timeout 2m --state-dir /var/run/docker/libcontainerd/containerd --shim docker-containerd-shim --runtime d... 
May 30 10:26:23 docker.r12dba.com dockerd[11481]: time="2018-05-30T10:26:23.307856900-04:00" level=info msg="libcontainerd: new containerd process, pid: 11486" 
May 30 10:26:24 docker.r12dba.com dockerd[11481]: time="2018-05-30T10:26:24.337794100-04:00" level=warning msg="failed to rename /var/lib/docker/tmp for background deletion: rename /var/lib/docker/tmp /var/lib/docker/t...g synchronously" 
May 30 10:26:24 docker.r12dba.com dockerd[11481]: time="2018-05-30T10:26:24.906599800-04:00" level=info msg="Graph migration to content-addressability took 0.00 seconds" 
May 30 10:26:24 docker.r12dba.com 
dockerd[11481]: time="2018-05-30T10:26:24.908583500-04:00" level=info msg="Loading containers: start." 
May 30 10:26:25 docker.r12dba.com dockerd[11481]: time="2018-05-30T10:26:25.826619700-04:00" level=info msg="Default bridge (docker0) is assigned with an IP address 172.17.0.0/16. Daemon option --bip can be used to set...rred IP address" 
May 30 10:26:26 docker.r12dba.com dockerd[11481]: time="2018-05-30T10:26:26.500678900-04:00" level=info msg="Loading containers: done." 
May 30 10:26:26 docker.r12dba.com dockerd[11481]: time="2018-05-30T10:26:26.536444300-04:00" level=info msg="Daemon has completed initialization" 
May 30 10:26:26 docker.r12dba.com dockerd[11481]: time="2018-05-30T10:26:26.537016800-04:00" level=info msg="Docker daemon" commit=d02b7ab graphdriver=overlay version=17.06.2-ol 
May 30 10:26:26 docker.r12dba.com systemd[1]: Started Docker Application Container Engine. 
May 30 10:26:26 docker.r12dba.com dockerd[11481]: time="2018-05-30T10:26:26.571771900-04:00" level=info msg="API listen on /var/run/docker.sock" Hint: Some lines were ellipsized, use -l to show in full. 
```
Verify that Docker is using the overlay2 storage driver. I will explain different storage drivers offered by Docker in a separate blog post at a later date. 
```
[root@docker docker]# docker info 
Containers: 0 Running: 0 Paused: 0 Stopped: 0 Images: 0 Server 
Version: 17.06.2-ol 
Storage Driver: overlay2 
Backing Filesystem: xfs 
Supports d_type: true Native Overlay 
Diff: true 
Logging Driver: json-file 
Cgroup Driver: cgroupfs 
Plugins: Volume: local Network: bridge host macvlan null overlay Log: awslogs fluentd gcplogs gelf journald json-file logentries splunk syslog 
Swarm: inactive 
Runtimes: runc Default Runtime: runc Init Binary: docker-init 
containerd version: 6e23458c129b551d5c9871e5174f6b1b7f6d1170 
runc version: 810190ceaa507aa2727d7ae6f4790c76ec150bd2 
init version: 949e6fa Security Options: seccomp Profile: default selinux 
Kernel Version: 3.10.0-862.3.2.el7.x86_64 Operating System: 
Oracle Linux Server 7.5 OSType: linux Architecture: x86_64 CPUs: 1 
Total Memory: 3.685GiB Name: docker.r12dba.com ID: AGY3:KEPT:DCAE:23HA:7WVS:45A7:4DMG:U4RH:Z5W7:EX4W:XKGY:DLGF 
Docker Root Dir: /var/lib/docker Debug Mode (client): false Debug Mode (server): false 
Registry: https://index.docker.io/v1/ Experimental: false Insecure Registries: 127.0.0.0/8 Live Restore Enabled: false 
```
Now we have all the stuff we need for Docker to be ready. Let's test by downloading an Oracle Linux 6 Docker image from public repo and get it up and running. 
```
[root@docker docker]# docker pull oraclelinux:6 
6: Pulling from library/oraclelinux 
b682776edd5e: Pull complete Digest: sha256:61e16cd44fca143b535855bf372aebc5d1d6bade44ff2fea8ce9c18c8f0927bf 
Status: Downloaded newer image for oraclelinux:6 
[root@docker docker]# 
[root@docker docker]# docker images 
REPOSITORY TAG IMAGE ID CREATED SIZE 
oraclelinux 6 9f9a3543f574 13 days ago 171MB 
[root@docker docker]# 
```
You can start this image by running the below command. We can pass the hostname to be used by the container as a command line argument. The below command brings up the container and runs the bash command and the "-it" command line argument makes it interactive. The container gets stopped as soon as you exit the bash command line. To make it run in the background, use detach "-d" command line 
```
[root@docker docker]# docker run -it --name oel6-1 -h oel6host.r12dba.com oraclelinux:6 bash 
[root@oel6host /]# 
[root@oel6host /]# hostname 
oel6host.r12dba.com 
```
Docker is a very interesting technology. My next blog post will look at how we can create an EBS Docker image and run Oracle EBS on Docker.