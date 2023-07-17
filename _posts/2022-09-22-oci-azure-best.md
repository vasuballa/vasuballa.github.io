---
layout: post
title: OCI & Azure — Best of both worlds ! 
description: Get to know how OCI and Azure work togeather
img: oci-azure.jpg # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [azure, oci]
comments: true
---

Many in Oracle and Microsoft domains, might have noticed the recent announcement about [Oracle DB service availability for Azure](https://blogs.oracle.com/cloud-infrastructure/post/announcing-oracle-database-service-for-microsoft-azure). Even before this was announced, it was feasible to connect Oracle Databases on OCI with Applications on Azure using the Low latency Fast-connect that has been available for a long time. Oracle and Azure have taken this one step further by providing a new OCI DBCS service that is now built into Azure itself. This allows Azure administrators to transparently provision Oracle Databases on OCI ExaCS, DBCS and Autonomous Database services without the need to login to OCI itself.

This is an exciting announcement that enables customers to take advantage of the best of both worlds. They will now be able to take advantages of the performance, reliability and data processing capabilities of Oracle Real Application Clusters, Exadata and Autonomous Database. At the same time deploy their application servers on Azure along with Analytical and BI tools such as PowerBI, that will connect directly to their Oracle Database services on OCI.

![Oracle E-Business Suite on OCI and Azure]({{site.baseurl}}/assets/img/oci-azure-detailed.webp)

Oracle E-Business Suite application is a good candidate for this multi-cloud architecture. Oracle E-Business Suite application servers can reside on Azure Linux VMs and the backend database hosted on OCI ExaCS or DBCS service. The massive ERP processing jobs and reporting jobs love the data processing power that ExaCS brings to the table. Exadata smart scan feature is a game changer in the database world. It optimizes SQL queries by filtering data at the storage level. If you have a massive size ERP or Data Warehouse database, then deploying the Database servers on OCI makes more sense.

In addition to the performance from OCI for the database layer, customers can take advantage Azure services like PowerBI or Tableau for their reporting needs. These Business Intelligence tools compliment well with Oracle ERP applications like Oracle E-Business Suite, PeopleSoft or JD Edwards, and will meet the reporting needs of customers.

One specific concern with this split multi-cloud deployment is network latency between App servers in Azure cloud and DB servers in OCI. Network latency is bound by laws of physics, the more the distance between the Azure and Oracle Data centers, the higher the latency will be. From our testing, the latency between OCI and Azure servers is in around the1ms range. This is a very very healthy latency and your users should not see any impact on the application responsiveness.

Reach out to us, if you are looking for top talent to deploy your application in a multi-cloud environment. We have the experience in deploying and configuring a low latency interconnect between OCI and Azure and tweaking the application to make it even more responsive.
