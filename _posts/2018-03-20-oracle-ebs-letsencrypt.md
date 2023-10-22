---
layout: post
title: Using LetsEncrypt Certs with Oracle E-Business suite
description: Using LetsEncrypt Certs with Oracle E-Business suite
img: letsencrypt.png # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [ssl, oracleebs, tls, letsecrypt]
comments: true
---

Enabling TLS/SSL is the first step of securing Oracle E-Business Suite, especially if you have internet facing DMZ nodes. TLS is what powers the Oracle EBS to use https URLs. LetsEncrypt makes this process easy. Let's Encrypt is a certificate authority that provides FREE X.509 certificates for Transport Layer Security (TLS) encryption via an automated process designed to eliminate the complex process of manual creation, validation, signing, installation, and renewal of certificates for secure websites. LetsEncrypt uses ACME protocol to validate and issue the certificate. There are numerous opensource tools that implement this ACME protocol to issue LetsEncrypt Certs. 

It uses two methods to verify that you control the domain. First method is URL validation. The tool will create an encrypted file on the webhost which is signed by your private key. LetsEncrypt server will access this URL to confirm that you control the domain and issue a certificate which is normally valid for only 90 days. You can renew this certificate anytime during the last 30 days to the expiry. This URL based validation will be most useful for Oracle E-Business Suite installations that have DMZ implementation with Internet facing modules likes iRec, iSupplier etc. As the DMZ url will be accessible from internet, it will be easy to get the URL verification done via ACME client tool. Second method of verification is DNS based. Instead of checking the URL, the LetsEncrypt server looks for specific TXT dns records which have encrypted messages signed by requester private key. 

This process is not as streamlined as URL validation method, But it is useful when we are requesting cert for a domain that has no internet facing hosts. As most Oracle E-Business Suite Servers are not exposed to the internet, DNS based validation method will be most useful for them. The tool I used is acme.sh . This tool has manual DNS validation method, basically it will wait for your admin to create a custom TXT DNS record with required values and then let LetsEncrypt verify the record. Here is snippet of how DNS validation works with acme.sh tool. #First run registered our account and directs us to create the TXT record

```
[oracle@apps letsencrypt]$ acme.sh --issue --dns -d ebs.r12dba.com
  [Thu Mar 8 23:11:26 EST 2018] Registering account
  [Thu Mar 8 23:11:27 EST 2018] Registered
  [Thu Mar 8 23:11:27 EST 2018] ACCOUNT_THUMBPRINT='xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx'
  [Thu Mar 8 23:11:27 EST 2018] Creating domain key
  [Thu Mar 8 23:11:27 EST 2018] The domain key is here: /home/oracle/.acme.sh/ebs.r12dba.com/ebs.r12dba.com.key
  [Thu Mar 8 23:11:27 EST 2018] Single domain='ebs.r12dba.com'
  [Thu Mar 8 23:11:27 EST 2018] Getting domain auth token for each domain
  [Thu Mar 8 23:11:27 EST 2018] Getting webroot for domain='ebs.r12dba.com'
  [Thu Mar 8 23:11:27 EST 2018] Getting new-authz for domain='ebs.r12dba.com'
  [Thu Mar 8 23:11:27 EST 2018] The new-authz request is ok.
  [Thu Mar 8 23:11:28 EST 2018] Add the following TXT record:
  [Thu Mar 8 23:11:28 EST 2018] Domain: '_acme-challenge.ebs.r12dba.com'
  [Thu Mar 8 23:11:28 EST 2018] TXT value: 'yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy'
  [Thu Mar 8 23:11:28 EST 2018] Please be aware that you prepend _acme-challenge. before your domain
  [Thu Mar 8 23:11:28 EST 2018] so the resulting subdomain will be: _acme-challenge.ebs.r12dba.com
  [Thu Mar 8 23:11:28 EST 2018] Please add the TXT records to the domains, and retry again.
  [Thu Mar 8 23:11:28 EST 2018] Please add '--debug' or '--log' to check more details.
  [Thu Mar 8 23:11:28 EST 2018] See: https://github.com/Neilpang/acme.sh/wiki/How-to-debug-acme.sh
  [oracle@apps letsencrypt]$ dig txt _acme-challenge.ebs.r12dba.com
# Second step is get network admin to create a TXT DNS record with name "_acme-challenge.hostname" # Verify that record got created with required value
; <<>> DiG 9.8.2rc1-RedHat-9.8.2-0.62.rc1.el6 <<>> txt _acme-challenge.ebs.r12dba.com
  ;; global options: +cmd
  ;; Got answer:
  ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 58420
  ;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0
 
 ;; QUESTION SECTION:
  ;_acme-challenge.ebs.r12dba.com. IN TXT
 
 ;; ANSWER SECTION:
  _acme-challenge.ebs.r12dba.com. 120 IN TXT "yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy"
 
 ;; Query time: 89 msec
  ;; SERVER: 7.7.7.1#53(7.7.7.1)
  ;; WHEN: Thu Mar 8 23:14:53 2018
  ;; MSG SIZE rcvd: 104
# Rerun the acme.sh tool to generate the certs
[oracle@apps letsencrypt]$ acme.sh --renew -d ebs.r12dba.com
  [Thu Mar 8 23:15:15 EST 2018] Renew: 'ebs.r12dba.com'
  [Thu Mar 8 23:15:16 EST 2018] Single domain='ebs.r12dba.com'
  [Thu Mar 8 23:15:16 EST 2018] Getting domain auth token for each domain
  [Thu Mar 8 23:15:16 EST 2018] Verifying:ebs.r12dba.com
  [Thu Mar 8 23:15:19 EST 2018] Success
  [Thu Mar 8 23:15:19 EST 2018] Verify finished, start to sign.
  [Thu Mar 8 23:15:20 EST 2018] Cert success.
  -----BEGIN CERTIFICATE-----
  aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
  aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
  aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
  aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
  aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
  aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
  aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
  aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
  aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
  aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
  aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
  aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
  aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
  aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
  aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
  aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
  aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
  aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
  aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
  aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
  aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
  aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
  aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
  aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
  aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
  aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
  aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
  -----END CERTIFICATE-----
  [Thu Mar 8 23:15:20 EST 2018] Your cert is in /home/oracle/.acme.sh/ebs.r12dba.com/ebs.r12dba.com.cer
  [Thu Mar 8 23:15:20 EST 2018] Your cert key is in /home/oracle/.acme.sh/ebs.r12dba.com/ebs.r12dba.com.key
  [Thu Mar 8 23:15:21 EST 2018] The intermediate CA cert is in /home/oracle/.acme.sh/ebs.r12dba.com/ca.cer
  [Thu Mar 8 23:15:21 EST 2018] And the full chain certs is there: /home/oracle/.acme.sh/ebs.r12dba.com/fullchain.cer
  [Thu Mar 8 23:15:21 EST 2018] It seems that you are using dns manual mode. please take care: The dns manual mode can not renew automatically, you must issue it again manually. You'd better use the other modes instead.
  [Thu Mar 8 23:15:21 EST 2018] Call hook error.
  [oracle@apps letsencrypt]$
```

The ACME client tool creates domain.KEY file which has the private key for the cert. domain.CER file which has the actual server certificate. CA.CER file which has the LetsEncrypt CA root cert that is used to sign the domain server certificate. fullchain.cer file which has both server and root certificates. To use these files with Oracle E-Business Suite, we have convert them to PKCS#12 format which is the standard used by oracle wallet's .p12 files . Use below commands to convert the files to Oracle EBS compatible p12 format

```
 [oracle@apps ebs.r12dba.com]$ pwd
 /home/oracle/.acme.sh/ebs.r12dba.com
 [oracle@apps ebs.r12dba.com]$ ls
 ca.cer cwallet.sso.lck ebs.r12dba.com.cer ebs.r12dba.com.conf ebs.r12dba.com.csr ebs.r12dba.com.csr.conf ebs.r12dba.com.key ewallet.p12.lck fullchain.cer
 [oracle@apps ebs.r12dba.com]$ openssl pkcs12 -export -out ebs.r12dba.com.p12 -inkey ebs.r12dba.com.key -in ebs.r12dba.com.cer -certfile ca.cer
 Enter Export Password:
 Verifying - Enter Export Password:
 [oracle@apps ebs.r12dba.com]$
 [oracle@apps ebs.r12dba.com]$
 [oracle@apps ebs.r12dba.com]$ cp ebs.r12dba.com.p12 ewallet.p12
 [oracle@apps ebs.r12dba.com]$
 
 [oracle@apps ebs.r12dba.com]$ $FMW_HOME/oracle_common/bin/orapki wallet display -wallet .
 Oracle PKI Tool : Version 11.1.1.9.0
 Copyright (c) 2004, 2015, Oracle and/or its affiliates. All rights reserved.
 
 Enter wallet password:
 Requested Certificates:
 User Certificates:
 Subject: CN=ebs.r12dba.com
 Trusted Certificates:
 Subject: CN=Let's Encrypt Authority X3,O=Let's Encrypt,C=US
 [oracle@apps ebs.r12dba.com]$
 
 
 [oracle@apps ebs.r12dba.com]$ $FMW_HOME/oracle_common/bin/orapki wallet create -wallet . -auto_login
 Oracle PKI Tool : Version 11.1.1.9.0
 Copyright (c) 2004, 2015, Oracle and/or its affiliates. All rights reserved.
 
 Enter wallet password:
 
 
 [oracle@apps ebs.r12dba.com]$ ls -ltr |tail -2
 -rw-r--r-- 1 oracle oinstall 4101 Mar 8 23:35 ewallet.p12
 -rw------- 1 oracle oinstall 4101 Mar 8 23:42 cwallet.sso
 [oracle@apps ebs.r12dba.com]$
```

One issue i noticed using this is, opmn and apache both crashed on first attempt to use this wallet. This is caused by LetsEncrypt root CAs not yet included in keystores used by Java JDK & JRE. To get around this, LetsEncrypt got its root certificate cross signed by another Certificate Authority "DST Root CA X3" that is recognized by most keystores. We need to download this "DST Root CA X3" root cert and include it in the oracle wallet to get around this opmn & Apache crash issue. The Apache crash issue is explained in this Oracle MOS Note "Oracle Http Server Fails To Start After Configuring Wallet With New Certificates, Error is "double free or corruption (out)" (Doc ID 2317291.1)"

```
 # download the cert from below url
 https://www.identrust.com/certificates/trustid/root-download-x3.html
 
 # Add the cert to oracle wallet
 
 [oracle@apps ebs.r12dba.com]$ $FMW_HOME/oracle_common/bin/orapki wallet add -wallet . -trusted_cert -cert ca2.cer
 Oracle PKI Tool : Version 11.1.1.9.0
 Copyright (c) 2004, 2015, Oracle and/or its affiliates. All rights reserved.
 
 Cannot modify auto-login (sso) wallet
 Enter wallet password:
 [oracle@apps ebs.r12dba.com]$ $FMW_HOME/oracle_common/bin/orapki wallet display -wallet .
 Oracle PKI Tool : Version 11.1.1.9.0
 Copyright (c) 2004, 2015, Oracle and/or its affiliates. All rights reserved.
 
 Requested Certificates:
 User Certificates:
 Subject: CN=ebs.r12dba.com
 Trusted Certificates:
 Subject: CN=Let's Encrypt Authority X3,O=Let's Encrypt,C=US
 Subject: CN=DST Root CA X3,O=Digital Signature Trust Co.
 [oracle@apps ebs.r12dba.com]$
```

After you have the wallet ready follow the steps in Oracle MOS Note "Enabling TLS in Oracle E-Business Suite Release 12.2 (Doc ID 1367293.1)" to enable TLS for Oracle E-Business Suite. Good Luck with TLS implementation in your EBS Environments. Do post your issues, comments and suggestions in this blog comments section!