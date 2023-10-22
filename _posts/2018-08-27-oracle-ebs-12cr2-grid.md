---
layout: post
title: Oracle Grid Infra 12cR2 ( 12.2 ) Certified for EBS Database
description: Oracle Grid Infra 12cR2 ( 12.2 ) Certified for EBS Database
img: orcl-ebs-19c.jpeg # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [19c, grid, oracle, oracleebs]
comments: true
---

Recenty, Oracle EBS ATG's team announced a new certification: ["Oracle Grid 12c Release 2 (12.2) Certified with RAC 12.1 and EBS 12.2"](https://blogs.oracle.com/ebstech/post/oracle-grid-12c-release-2-122-certified-with-rac-121-and-ebs-122). This is a very new kind of certification, which is different from their previous certifications related to RAC databases. Until now, Oracle EBS only certified Oracle RAC software as a whole i.e., Oracle RAC database 12.1 along with Grid Infrastructure 12.1 is certified for use with EBS Database. Whereas in this current certification announcement, they only certified Grid Infra 12.2 without the RAC 12.2 database part. 

This means Oracle EBS customers now can run Oracle EBS RAC Database version 12.1 with Grid Infra Version 12.2, breaking their tradition of keeping both RAC & Grid at the same software level. Actually, this is a welcome relief to users running Oracle EBS Databases on engineered systems such as Exadata and Oracle Database Appliance. Let me explain. Oracle Database version 12.1 went from Premier support to Extended support in July 2018. Even though Oracle announced that they are waiving fees for Extended support until July 2019, many customers are starting to upgrade their databases environments to version 12.2. This became a problem for engineered systems customers who consolidated all their databases on to Exadata or ODA because EBS was not certified with database version 12.2, which means customers cannot run EBS Database on Exadata which has been upgraded to 12.2. 

A lot of DBAs hoped they could keep EBS database at version 12.1 and upgrade rest of the databases and Grid Infra to 12.2 on Exadata, as it is certified by the database team. But after an SR with Oracle EBS Team, their hopes were shot down, as the EBS team needs both GI and RAC to be at same version level for support. Our engineered systems had to put all their database upgrade plans on hold just because of EBS Database running on their Exadata. And obviously, they voiced their displeasure to Oracle EBS Team. This new certification gives relief to these customers. They will now be able to upgrade all their non-EBS databases to 12.2 along with Grid Infra and keep their EBS database at 12.1. I wish they would release a full certification of both Grid Infra and Database 12.2, but for now, I will have to settle for this half certification. Good luck with upgrading your systems to 12.2 !!!