---
layout: post
title: Oracle E-Business Suite’s New EBS_SYSTEM Schema
description: Learn about new Oracle EBS EBS_SYSTEM user
img: oracle-ebs-erp.png # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [oracleebs, oracle, erp]
comments: true
---

For a long time, after the introduction of Online patching with EBS 12.2 release, there were no major changes to the underlying database architecture in Oracle E-Business Suite. With Oracle EBS 12.2, Edition based redefinition (EBR) feature was introduced to power the Online Patching feature. Online patching feature helped reduce the downtime requirements of E-Business Suite by enabling the ability to apply the major part of any E-Business Suite patch while the system is up and running, hence the name Online patching.

![Oracle EBS Database Schemas]({{site.baseurl}}/assets/img/oracle-ebs-system.gif)

For a long time, Oracle E-Business Suite Schema remained consistent and looked like the above table, with R12.2 adding some EBR specific objects such as Edition Views and Triggers. However, with EBS 12.2 ATG_PF Delta13 patch, the Oracle EBS development team introduced a new schema called EBS_SYSTEM. Interestingly, this schema’s password needs to be kept the same as the current database System user. I wondered why the Oracle Dev Team introduced this schema and here is my take on it.

Lots of people think this change was introduced to address segregation of duties and the separation of the elevated privileges required to maintain Oracle EBS schemas into a separate admin account similar to System/Sys schema. This could be true, but I think it’s more than that. I believe the change was made because of new PDB requirements in 19c.

Oracle EBS was certified to run on Oracle 19c database some time back. One of the requirements as part of this certification is that the Oracle EBS database will be converted into a PDB during the 19c database upgrade. At a high level, the process is to install a fresh 19c CDB and use Oracle supplied scripts to upgrade the EBS database to a PDB and attach it to the freshly installed CDB. One limitation in the current EBS 19c database certification is that we cannot attach any other PDBs to the CDB which has the EBS PDB attached. Thus, even though the EBS database is converted to a PDB, customers cannot take advantage of multi-tenant architecture, as they cannot attach other PDBs to EBS Container Database.

I think that the EBS_SYSTEM schema implementation address this 19c CDB/PDB limitation. Once Oracle EBS Dev team moves all dependencies from SYSTEM schema to EBS_SYSTEM schema, Oracle EBS database should be able to take full advantages of PDB/CDB architecture. Having a different local PDB admin account to take care of all EBS specific needs will remove dependency on CDB system schema and EBS Database should become portable. This change will help in EBS databases becoming more DBcS friendly. It will be even be possible to host Oracle EBS in AWS RDS if these PDB limitations are lifted. We just have to wait and see how this plays out.

Do let me know your thoughts and comments on this subject. I would love to hear what you think about Oracle EBS PDB limitations and progress.