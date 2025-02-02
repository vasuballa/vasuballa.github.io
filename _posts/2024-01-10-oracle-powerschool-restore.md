---
layout: post
title: Lessons from PowerSchool Oracle Database Restore
description: Lessons learned and recommendations from Oracle database restore  
img: oracle-321.png # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [oracle, database, backup-restore, powerschool]
comments: true
---

This blog is my musing from my recent client visit. I recently got summoned to help a client restore services after a cyberattack incident. This client is running Oracle PeopleSoft for their payroll and financials, and PowerSchool as their student information system. PowerSchool was in the news recently about a [data breach](https://www.powerschool.com/security/sis-incident/notice-of-united-states-data-breach/), which seems to have caused cascading data breaches at multiple customers. It’s speculation as of now, as investigations are still in progress. Coming back to our applications that got affected, all these applications are using Oracle Database as their backend, which is world-class database software that comes with all the tools and procedures required to recover from backups.

This customer situation turned out to be more complex than I was expecting. Due to multiple small mistakes by different teams, the Oracle RMAN backup of the PowerSchool database did not get picked up by their backup software. What this customer experienced can be labeled as a complex failure, as described by Amy Edmondson in her book ["Right Kind of Wrong"](https://amazon.ca/Right-Kind-Wrong-Science-Failing/dp/1982195061/?tag=linustechti06-20&geniuslink=true). A must-read, by the way. Complex failures arise from a combination of factors rather than a single error and often involve interactions between different systems or processes. Let's not get into who is responsible for their Oracle backup not making it into their backup solution. As I said, it's a complex failure involving multiple teams and processes. But there was good news at the end: we managed to find a Data Pump backup and restored the system using that.

But this situation made me think deeply about backups in the new ransomware attack world. In a ransomware attack, typically intruders, after gaining access to your system, will wipe your servers and encrypt critical data and backup files, and ask for a ransom to be paid in bitcoins. Usually, administrators follow the 3-2-1 thumb rule when dealing with any critical system backup. The rule goes like this:

1. 3 Copies of Your Data: Keep at least three copies of your data. This includes the original data and two backups. This redundancy helps protect against data loss.
2. 2 Different Storage Media: Store the copies on at least two different types of storage media. For example, you might use an external hard drive and cloud storage. This reduces the risk of both backups failing simultaneously.
3. 1 Offsite Backup: Keep at least one copy offsite. This means storing a backup in a different physical location, such as a cloud service or a remote server. This protects your data from local disasters like fires or floods.

Even with good backups, Intruders can hold this at ransom. I think we should add two more points to this rule to deal with ransomware attacks. 

The first one is having an immutable backup solution. An immutable backup solution ensures that intruders cannot delete or encrypt your backups. This way, you always have a way to restore your backup when everything else is lost.

The second one is to test your backups. Have a quarterly or bi-annual test restore of your production backups to make sure that they are recoverable, and you have a documented way of restoring a full system from backups. Having a tested and practiced restore procedure will help the DBA not get stressed about bringing the system back online and meet the RPO/RTO objectives of the system. It's just like schools and offices doing fire drills on a regular basis. You prepare for the worst and hope for the best!

Any questions and comments about this blog, reach out to me via [Linkedin](https://linkedin.com/in/vasuballa)

