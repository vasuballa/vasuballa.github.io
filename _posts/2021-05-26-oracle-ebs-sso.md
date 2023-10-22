---
layout: post
title: New Single Sign-on (SSO) Solution for Oracle E-Business Suite (EBS)
description: New Single Sign-on (SSO) Solution for Oracle E-Business Suite (EBS)
img: oracle-ebs-erp.png # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [oracleebs, oracle, sso]
comments: true
---

Because security is a crucial part of managing your data, we’re pleased to tell you about the identity management (IdM) solution we’ve developed to make it easier for Oracle E-Business Suite (EBS) clients to implement SSO.

## Background
Single sign-on (SSO) authentication, and identity management in general, have come a long way in the last 10 years. For the last decade, Microsoft Active Directory has been the most popular identity service. But in the last three years we’ve seen cloud-based identity management (IdM) products become more popular. These include OKTA, Azure ADFS, Onelogin, Ping Identity and Oracle’s own Identity Cloud Service ( IDCS). These cloud-based IdM products have made methods like single sign-on and multi-factor authentication (MFA) very simple to implement for web applications using SAML (Security Assertion Markup Language) and OAuth protocols.

## Existing SSO options for Oracle EBS applications
When it comes to single sign-on options for Oracle E-Business Suite (EBS) applications, not a lot has changed. Originally, there was only one solution to enable single sign-on for EBS applications with products like Microsoft Active directory. You had to use Oracle Access Manager (OAM) with Oracle Internet Directory (OID). These two products handled the authorization and user-provisioning aspects of EBS applications respectively.

## OAM/OID products
When it comes to enabling SSO for EBS applications with cloud-based identity products like OKTA and Ping, you’re still forced to integrate via OAM/OID products. So you end up implementing the OAM/OID suite, which is part of the Oracle Identity Management suite, thus incurring extra costs in terms of licensing and support fees.

## Oracle’s EBS Asserter
Well, there’s an exception to that. For Oracle’s own cloud-based identity management product, IDCS, there’s no need to implement the OAM/OID suite to enable SSO for EBS. Oracle released a small Java-based app called EBS Asserter which you can deploy free of cost on a WebLogic 12c server to enable SSO for EBS with IDCS. This EBS Asserter doesn’t work with other cloud identity management products—it’s exclusive to Oracle IDCS.

## Solution
Now, We have come up with a solution similar to EBS Asserter. We’ve used the open-source Apache HTTP server along with the auth-mellon module to implement SSO for EBS. Our solution works with all cloud-based identity management products including OKTA, Azure ADFS, Onelogin, Google Workspace and Ping Identity. Because this solution requires no OAM/OID stack, it saves you a lot of money in terms of Oracle license fees. I’ll provide a more technical overview of our EBS SSO solution in my next post.

If you have plans to implement EBS SSO with cloud IdM products such as OKTA, ADFS, Ping Identity or others, please reach out to us for more information.