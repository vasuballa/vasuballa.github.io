---
layout: post
title: Oracle Cloud Data breach Action Plan
description: Recommendations for mitigating risks from Oracle Cloud Data breach
img: oci_breach.png # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [oracle, cloud, databreach, ldap]
comments: true
---

Though Oracle officially denies the data breach, the evidence posted online and forums speaks for itself. The original blog that peaked my interest is from [CloudSEK](https://www.cloudsek.com/blog/the-biggest-supply-chain-hack-of-2025-6m-records-for-sale-exfiltrated-from-oracle-cloud-affecting-over-140k-tenants). So i dug around and found the forum where the original sample files are posted. I analyzed the 3 files, a sample LDAP file, a sample database dump file and a list of company domains extracted from the ldap. 

In my 20 year IT career, I have handled multiple projects where we used Oracle Identity management products (OAM/OID/OUD). The data shared fits the bill for data exported from a Oracle Internet Directory ldap server and its backend oracle database schema. the Ldap schema and database table columns and sample data all look legit. I verified the names, domains and tenancies, as well cross references names of our customer who use Oracle. Many of them are there in the data shared, which makes me believe the breach is legit and its time to action. Also Oracle did a expedited MFA rollout for "My Oracle Support" portal just last week with very short notice, which is very unlike of them. 

The breached data includes corporate email ids and passwords used with Oracle Cloud Identity domain (login.us2.oraclecloud.com) which seems to running 11g Oracle Identity management services. For quite sometime, oracle was moving customers from old IDCS tenancies to new IAM domains, which is their new generation Identity Cloud services. Customers who signed up in the past 1-2 years, are already on new IAM domains. It looks like only customer which are originally on IDCS tenancies seems to be expected. But no matter what, here is the action plan i recommend

As Hashed passwords are part of the ldap dump, Change your oracle.com accounts used for oracle support portal which is used primarily used to download software, patches, log SRs etc. You can do that by going to [profile.oracle.com](https://profile.oracle.com) 

Oracle Cloud Infrastructure and Oracle Cloud SaaS (ERP, HCM, SCM & Retail etc) customers use IDCS and IAM domains for their authentication. For accounts in these services, that dont have Federation with Sigle Signon with external IdP like OKTA, Azure AD/EntraID, please change the passwords and ensure you have OCI MFA enabled. If you have Federation and Single Signon, but still using old Oracle Identity Cloud Service (IDCS), make sure you rotate your SAML certificaes and client secret keys. 

Last, But not least, with any data breach, the risk of Credential stuffing increases. Credential stuffing is a cyberattack where attackers use stolen usernames and passwords, often from data breaches, to try and log into other unrelated systems, exploiting the fact that many users reuse credentials across multiple accounts. So remind your users not to reuse passwords across different services and use password manager to store their unique passwords.

Good luck ! And any questions and comments about this blog, or help with securing your oracle cloud tenancy reach out to me via [Linkedin](https://linkedin.com/in/vasuballa)

