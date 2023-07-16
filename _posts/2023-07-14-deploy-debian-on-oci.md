---
layout: post
title: Deploy Debian on Oracle Cloud Infrastructure 
description: Get to know how to deploy Debian offical image on OCI
img: debian.jpg # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [debian, oci]
comments: true
---

Recent RedHat debacle around their source code avilability made me wonder about the real free alternatives out there that on can run on the cloud platforms. Debian is no doubt the best real free ( as in freedom) linux distribution out there. And i wanted to try it out on Oracle Cloud Infrastructure (OCI). Unfortunately, Debian image is not available out of the box on OCI. This post describes the steps to get it up and running on OCI.

## Debian offical cloud image

Luckily Debian provides offical images that one can download at [cloud.debian.org](https://cloud.debian.org/images/cloud) . Debian provides the images in various formats that can be used on AWS, GCP, Azure as well as OCI. OCI supports importing qcow2 image that Debian provides. 

## Import Debian image to OCI

Grab the generic cloud image [debian-12-genericcloud-amd64.qcow2](https://cloud.debian.org/images/cloud/bookworm/latest/) and upload to the object storage bucket. Once its in the bucket, you can import it to OCI using custom image import procedure as described at [docs.oracle.com](https://docs.oracle.com/en-us/iaas/Content/Compute/Tasks/importingcustomimagelinux.htm)

From my testing, the image worked without any issues. Instead of default opc user, we need to use debian user to login to the VM once launched. The ssh key we upload during the instance creation, worked with the debian user to login.

Hope this blog helps others in getting Debian running on OCI.
