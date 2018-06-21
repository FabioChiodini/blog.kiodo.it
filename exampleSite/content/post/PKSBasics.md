---
title: First steps with Pivotal Container Service (PKS)
date: 2018-06-19 07:58:40 +0000
tags:
- BOSH
- NSX-T
- Pivotal
- PKS
categories:
- Kubernetes
- Containers
description: Tips and Tricks I discovered installing Pivotal Container Services (PKS)
cover: https://raw.githubusercontent.com/FabioChiodini/blog.kiodo.it/master/images/PKSCover.png
draft: false

---
I like Kubernetes and Containers so I set out to install Pivotal container service (PKS).

I followed some good blog posts so I am going to provide link to them and add some commands and tricks that I found useful (just go to the paragraph marked as **my field notes**.

# What's PKS?

Pivotal Container Service (PKS) is a purpose-built product that enables enterprises and service providers to simplify the deployment and operations of Kubernetes clusters. It provides a production-grade Kubernetes distribution with deep VMwareNSX-T integration for advanced networking, a built-in private registry with enterprise security features and full life cycle management support of the clusters.

# What do You need?

Get some coffee and have a VMware environment available. I used my lab: a few ESXi hosts manager by vCenter. Go here for the versions:

[https://docs.pivotal.io/runtimes/pks/1-0/#prerequisites](https://docs.pivotal.io/runtimes/pks/1-0/#prerequisites "PKS Prerequisites")

# Installation Overview

If you want to leverage the full power of PKS (ie all integrations) this is the high level install process:

* Fix prerequisites
* Install NSX-T for managing K8S network constructs and add-ons
* Prepare NSX-T constructs for PKS
* Install Ops Manager and **BOSH**
* Install PKS
* Deploy a **Kubernetes Cluster**
* Install Harbor to manage container images
* Install VMware vRealize Log Insight (vRLI) to get the **logs** from your environment
* Install VMware vRealize Operations Manager (vROps) to monitor your infrastructure

# Resources I have used

Here are the blog posts that helped me doing a _manual_ setup of PKS (it can be automated, that will be for another blog post):

[https://www.virtuallyghetto.com/2018/03/getting-started-with-vmware-pivotal-container-service-pks-part-1-overview.html](https://www.virtuallyghetto.com/2018/03/getting-started-with-vmware-pivotal-container-service-pks-part-1-overview.html "William Lam Blog")

As this blog post may appear daunting you may start reading a smaller version provided by the always amazing Cormac Hogan:

[https://cormachogan.com/2018/04/24/a-simple-pivotal-container-service-pks-deployment/](https://cormachogan.com/2018/04/24/a-simple-pivotal-container-service-pks-deployment/ "Faster setup")

## My field notes

Start small and make sure the environment is stable. Take your time to setup NSX-T. After that BOSH will help you a lot in uninstalling/reinstalling if needed ;)

# Installing NSX-T

The installation instructions for this this have been well-documented by my good friends at VMware so you can refer to the previous posts.

I especially recommend this if you are totally new to NSX-T:

[https://cormachogan.com/2018/04/11/building-a-simple-esxi-host-overlay-network-with-nsx-t/](https://cormachogan.com/2018/04/11/building-a-simple-esxi-host-overlay-network-with-nsx-t/ "NSX-T simpler")

## My field notes

I have largely simplified the setup provided by  Mr Lam but I was able to see all the NSX-T network integrations available.

Here's a detailed overview of my Lab

![](https://raw.githubusercontent.com/FabioChiodini/blog.kiodo.it/master/images/PKSLab.png)

As You can see I've collapsed the admin and management network on a single segment (not managed by NSX-T) and I am using BGP pairing with my Lab router to auto propagate route information from NSX-T.

### BGP Setup

In my setup I am using Vyatta as the main router.

BGP setup was very easy on the NSX-T side:

![](https://raw.githubusercontent.com/FabioChiodini/blog.kiodo.it/master/images/BGPNSX-T.png)

But it was quite easy even on the Vyatta part:

    configure
    
    set protocols bgp 5500 neighbor 10.64.167.166 remote-as 999
    
    set protocols bgp 5500 network 10.10.10.0/23
    
    set protocols bgp 5500 network 10.64.144.0/24
    
    set protocols bgp 5500 neighbor 10.64.167.166 password somePWD
    
    commit
    
    save

### BGP Troubleshooting

Here are a few command to test if BGP is working:

Connect as admin to the NSX-T edge

Find the router using

    get logical-routers

Find the T0 service router vrf

Change to the vrf

    vrf 2

use the commands

    get bgp neighbour

    get bgp

![](https://raw.githubusercontent.com/FabioChiodini/blog.kiodo.it/master/images/NSXBGPCLI.png)

To check in on the Vyatta side just use:

    show ip bgp

![](https://raw.githubusercontent.com/FabioChiodini/blog.kiodo.it/master/images/VyattaBGP.png)

### Adding a static route to NSX Manager

The NSX manager must be reachable by all the elements that you see in the diagram.

If you messed up (like I did :P ) you can still edit the routes by using a command line this

    set route prefix 0.0.0.0/0 gateway 10.64.167.254

\[Just login as admin on the Manager VM and execute it\]

# Troubleshooting BOSH

if you are new to BOSH I want to provide You with some basic command to troubleshoot it.

So here are some examples ;)

## Getting the logs from a bosh deployment

if your BOSH deployments is misbehaving you should definitely check the extensive logs provided by it.

First determine the deployment name with

    bosh deployments

## PKS Deployment Troubleshooting

If you get an error like this in your BOSH logs:

    I0615 08:19:22.962041   12746 cloudprovider.go:59] --external-hostname was not specified. Trying to get it from the cloud provider.

    error setting the external host value: "vsphere" cloud provider could not be initialized: could not init cloud provider "vsphere": 3:6: unquoted '\' must be followed by new line

Then I have a fix for You ;)

If you are using a domain account make sure you are using the user@domain notation and **no**_t_ the domain\\account one:

![](https://raw.githubusercontent.com/FabioChiodini/blog.kiodo.it/master/images/PKSErrorSlash.png)

# Conclusion

It was a great learning experience so hopefully the same applies to You. Stay tuned for more :)