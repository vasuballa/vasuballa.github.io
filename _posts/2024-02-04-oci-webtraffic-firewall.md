---
layout: post
title: Firewall for Outgoing Layer7 Web traffic on OCI
description: Filter traffic going via NAT gateway or Internet gateway on OCI
img: oci-logo.png # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [firewall, oci, ubuntu, zenarmor]
comments: true
---

The default firewall features in Oracle Cloud Infrastructure (OCI) are provided using Security Lists(SL) and Network Security Groups(NSG). These work on Layer3 level that is they filter traffic based on to/from IP Address of the host. But in most cases, Customers want to block all outgoing internet traffic and open traffic only to specific URLs like yum repos, public cloud based application URLs for REST API integrations. Fullfilling this requirement using SL & NSG is very difficut, as IP addresses of Cloud hosted applications sometimes keep changing because of CDN usage. A better way to meet this requirement is to use a firewall that provides layer7 filting like Fortigate, Palo Alto etc. Oracle has their own offering in this area called OCI Network Firewall Service. OCI NFW costs about $2500 a month. Forigate and Paloalto are not much cheap either. Their price ranges from $500 to $1500 a month. This made me look for other opensource/commercial solutions that are much cheaper to deploy and use. Below architeture describes a solution that provides layer7 filtering while being light on your wallet. 

This solution uses [Zenarmor](https://www.zenarmor.com/zenarmor-secure-web-gateway), a Secure web gateway product from Sunny Valley Cyber Security Inc running on a ubuntu VM. This setup only costs abouts $50 a month. Here is how the architecture loks like

![Zenarmor Web Gateway on OCI]({{site.baseurl}}/assets/img/oci-zenarmor-nfw.png)


To deploy this architecture, here are the high level steps

1. Create a public subnet that firewall VM uses to send traffic out to Internet
2. Create a private subnet that firewall receives that traffic from Internal IPs that is destined to the internet
3. Deploy a Ubuntu 22.04 LTS VM on public subnet which will be used as Firewall 
4. Attach a VNIC from private subnet to Ubuntu Firewall VM
5. Configure the Ubuntu to route traffic from Private Subnet to Public Subnet
6. Update routing table of private subnet & pubic subnet to make traffic goes via Firewall VM private nic
7. Deploy and Activate Zenarmor Agent on Ubuntu Firewall VM
8. Enable URL filtering using polices on Zenarmor dashboard
9. Update route tables of private subnets to point the 0.0.0.0/0 route to Firewall Private IP 


## Create Subnets

Create required subnets as follows. App & DB Subnet will be private to host the respective VMs. IN and OUT subnets will be used by the firewall VM. IN subnet will be private and used to receive inbound traffic from other private subnets (App & DB) that is to be passed to the internet

|**Subnet Name**|**CIDR Block**|**Type**|**Description**|
|:-----------:|----------|:----------:|-----------|
|app|10.0.0.0/28|Private|Subnet to host App VM|
|db|10.0.0.16/28|Private|Subnet to host Database|
|in|10.0.0.96/28|Private|FW incoming subnet|
|out|10.0.0.112/28|Public|FW outbound subnet|

## Create Firewall VM

Deploy a VM with Ubuntu latest 22.04 LTS image on VM.Standard.E4.Flex shape. You can use a burstable instance to save on cost as well. Make sure to assign the nic in public subnet (OUTPUT subnet) as the primary nic and assign a second nic later from the private subnet (IN subnet). 

![FW VM NIC Details]({{site.baseurl}}/assets/img/oci-fw-img1.png)


## Configure second nic and ubuntu firewall

Configure primary nic that is in the OUT subnet to use dhcp, and second nic to use fixed IP.

```
$ cat /etc/netplan/50-cloud-init.yaml
 network:
    version: 2
    ethernets:
        ens3:
            dhcp4: true
            match:
                macaddress: 02:00:17:01:38:e4
            set-name: ens3
        ens5:
            match:
                macaddress: 02:00:17:00:c7:55
            addresses:
              - 10.0.0.100/28
            set-name: ens5
```

Once you have the NICs configured in the VM, configure the ubuntu built-in firewall to route traffic from private nic (IN) to public nic (OUT). This guide hosted on github helped me configure it - [https://gist.github.com/kimus/9315140](https://gist.github.com/kimus/9315140)

## Install Zenarmor

Run the one-liner to install the package
```
$ curl https://updates.sunnyvalley.io/getzenarmor | sudo sh
```

Register an account in Zenconsole Cloud Portal. Connect and start managing through Zenconsole
```
# zenarmorctl cloud register
```

After the zenarmor agent is installed, go to the zenarmor dashboard and configure the firewall to be in L3 routed mode, tag ens3 as wan interface and tag ens5 as lan interface as shown below

![Zenarmor config]({{site.baseurl}}/assets/img/oci-fw-img6.png)


## Configure the route tables

Create Two route tables. One for outbound subnet that routes all traffic to the internet gateway. Assign this route table to OUT subnet

![Outbound Subnet Route table]({{site.baseurl}}/assets/img/oci-fw-img2.png)

Second route table that is assigned to inbound subnet that routes all traffic to private IP of the Firewall NIC 

![Inbound Subnet Route table]({{site.baseurl}}/assets/img/oci-fw-img3.png)

Make sure to assign the inbounce route table to all private subnets ( IN, APP, DB subnets)

## Configure Zenarmor policy

Configure the policy attached to the registed VM and set what URLs to block and What URLs to allow

![Zenarmor Policy]({{site.baseurl}}/assets/img/oci-fw-img4.png)


## Test 

Above policy blocks any connections to r12dba.com and allows connections to eclipsys.ca. Lets test it from a app VM that is deployed on APP subnet

![Test results]({{site.baseurl}}/assets/img/oci-fw-img5.png)


As you can see, the connection to r12dba.com got dropped and we can got response from eclipsys.ca. 

## Conclusion

We dont need fancy firewalls that cost an arm and a leg to achieve this Layer7 filtering. We can achieve this easily and cost effectivelyu with a Ubuntu VM along with Zenarmor subscription. Any questions about this solutions, reach out to me via [Linkedin](https://linkedin.com/in/vasuballa)


