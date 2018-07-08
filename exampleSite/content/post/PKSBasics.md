---
title: First steps with Pivotal Container Service (PKS)
date: 2018-06-20 07:58:40 +0000
tags:
- Troubleshooting
- BOSH
- NSX-T
- Pivotal
- PKS
categories:
- Kubernetes
- Containers
description: Tips and Tricks I discovered installing Pivotal Container Services (PKS)
cover: https://raw.githubusercontent.com/FabioChiodini/blog.kiodo.it/master/images/PKSCover.png

---
I like Kubernetes and Containers so I set out to install Pivotal container service (PKS).

I followed some good blog posts so I am going to provide link to them and add some commands and tricks that I found useful (just go to the paragraph marked as **my field notes**.

Let's start with the usual **disclaimer** :P This is meant to be as a **Lab/Learning setup** not a production one so if You want to deploy to production You'll have to take into account other non trivial factors depending on your environment (ie Design it properly!! :P )

# What's PKS?

Pivotal Container Service (PKS) is a purpose-built product that enables enterprises and service providers to simplify the deployment and operations of Kubernetes clusters. It provides a production-grade Kubernetes distribution with deep VMware NSX-T integration for advanced networking, a built-in private registry with enterprise security features and full life cycle management support of the clusters.

It is not just "yet another Kubernetes distribution" but it provides more a "Kubernetes as service experience.

What do I mean by that? Using an API (PKS API) you can just ask for a Kubernetes Cluster and PKS will provision one for You. Not only that but it will let you manage/upgrade it in Day 1 and 2 and also integrate it with the right networking/monitoring/logging/security add-ons that you need in a production environment.

If you want more details just go [here](https://content.pivotal.io/blog/secure-multitenant-kubernetes-in-minutes-pivotal-container-service-goes-ga "HERE") or [here ](https://www.youtube.com/watch?v=bQKra0CB5zE)if you like videos better :)

# MY point of View

**So why should I care about an API that helps me in managing Kubernetes?**

I have a long background in Operations but I see value in this "software thing" here :)

**If you have an API on top of your Kubernetes deployment you have the best interface to _operate it _**: you can move away from manual operations and put (more) automation in place.

If something happens and you want to change how your Kubernetes cluster is behaving you no longer have to stick to configuration files but you can **just have a machine talk to PKS and change Kubernetes configuration/behaviour accordingly.**

This could be the "infamous" scale up/scale Down to accommodate peaks but it could also be some remediation action even some security procedure or whatever will be needed in the future.

**And all of these could be automated freeing you up from manual tasks (and errors as well) :)**

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
* Install Harbor to **manage container images**
* Install VMware vRealize Log Insight (vRLI) to get the **logs** from your environment
* Install VMware vRealize Operations Manager (vROps) to **monitor your infrastructure**
* Connect your setup to VMware Wavefront to get application performance monitoring (and more Kubernetes monitoring as well)

# Resources I have used

Here are the blog posts that helped me doing a _manual_ setup of PKS (it can be automated, that will be for another blog post).

Full setup as described by William Lam ( @lamw ):

[https://www.virtuallyghetto.com/2018/03/getting-started-with-vmware-pivotal-container-service-pks-part-1-overview.html](https://www.virtuallyghetto.com/2018/03/getting-started-with-vmware-pivotal-container-service-pks-part-1-overview.html "William Lam Blog")

As this blog post may appear daunting you may start reading a smaller version provided by the always amazing Cormac Hogan ( @CormacJHogan ):

[https://cormachogan.com/2018/04/24/a-simple-pivotal-container-service-pks-deployment/](https://cormachogan.com/2018/04/24/a-simple-pivotal-container-service-pks-deployment/ "Faster setup")

## My field notes

Start small and make sure the environment is stable.

Take your time to setup NSX-T.

After that BOSH will help you a lot in uninstalling/reinstalling if needed ;)

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

In my setup I am using **Vyatta** as the main router.

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

Connect as **admin** to the NSX-T edge

Find the router using

    get logical-routers

Find the T0 service router vrf

Change to the vrf

    vrf 2

use the commands

    get bgp neighbour
    
    get bgp

![](https://raw.githubusercontent.com/FabioChiodini/blog.kiodo.it/master/images/NSXBGPCLI.png)

To check in on the **Vyatta** side just use:

    show ip bgp

![](https://raw.githubusercontent.com/FabioChiodini/blog.kiodo.it/master/images/VyattaBGP.png)

### Adding a static route to NSX Manager

The NSX manager must be reachable by all the elements that you see in the diagram.

If you messed up (like I did :P ) **you can still edit the routes** by using a command line this

    set route prefix 0.0.0.0/0 gateway 10.64.167.254

\[Just login as admin on the Manager VM and execute it\]

# Troubleshooting BOSH

if you are new to BOSH I want to provide You with some basic command to troubleshoot it.

So here are some examples ;)

## Getting BOSH status

You can use the command

    bosh instances

to get an **overview of your deployed environment**:

![](https://raw.githubusercontent.com/FabioChiodini/blog.kiodo.it/master/images/BOSHINSTANCES.png)

You can even get **more details** (performamnces/load) by using:

    bosh instances --ps --details --dns --vitals

## Getting IPs and VM names

if you need to figure out the **name of the VMs in vCenter or the IPs** to test connectivity here's your fiend:

    bosh vms

![](https://raw.githubusercontent.com/FabioChiodini/blog.kiodo.it/master/images/BOSHVMS.png)

## Getting the logs from a bosh deployment

if your BOSH deployments is misbehaving you should definitely check the extensive logs provided by it.

First determine the deployment name with

    bosh deployments

You can then **get the deployment logs** using:

    bosh logs -d deploymentname

![](https://raw.githubusercontent.com/FabioChiodini/blog.kiodo.it/master/images/BOSHLOGSCLI.png)

Than **just untar the logs** and go through  them ;)

## Getting events for your deployments

To troubleshoot your deployments you may need to have a look at the related **events** via:

    bosh events --deployment deploymentname

![](https://raw.githubusercontent.com/FabioChiodini/blog.kiodo.it/master/images/BOSHEVENTS.png)

## BOSH Cloud Check

You can also c**heck the VMs that make up one deployment** by using:

    bosh cloud-check -d deploymentname

![](https://raw.githubusercontent.com/FabioChiodini/blog.kiodo.it/master/images/BOSHCLOUDCHECK.png)

## Getting information on a specific BOSH task

To check information about a specific BOSH task use the syntax

    bosh task tasknumber

![](/uploads/BOSHtask.png)

## PKS Deployment Troubleshooting

If you get an error like this in your BOSH logs:

    I0615 08:19:22.962041   12746 cloudprovider.go:59] --external-hostname was not specified. Trying to get it from the cloud provider.
    
    error setting the external host value: "vsphere" cloud provider could not be initialized: could not init cloud provider "vsphere": 3:6: unquoted '\' must be followed by new line

Then I have a fix for You ;)

If you are using a domain account make sure you are using the user@domain notation and **no**_t_ the domain\\account one:

![](https://raw.githubusercontent.com/FabioChiodini/blog.kiodo.it/master/images/PKSErrorSlash.png)

## BOSH Authentication errors

If you mess up (like I did) you may find yourself **unable to authenticate to BOSH remotely via CLI.**

No worries this flag may come to your rescue:

![](https://raw.githubusercontent.com/FabioChiodini/blog.kiodo.it/master/images/BOSHRecreate.png)

Just flag it and apply changes, this will rebuild the VMs (you won't lose data thanks to BOSH automation and persistent disks ;) ).

I found out that it will fix the inconsistency.

**Remember** that after the redeploy the authentication token **BOSH_CLIENTSECRET** will change so obtain a new one using:

    om --target https://opsmanager.vmlive.italy -u admin -p 'YourPWD' -k curl -p /api/v0/deployed/director/credentials/bosh2_commandline_credentials -s | jq -r '.credential'

\[Just imagine doing this without a tool like BOSH or on a fully manual installation of Kubernetes :P \]

## More troubleshooting Links

If you experience other errors here's a [good link](https://cormachogan.com/2018/04/26/pks-networking-setup-tips-and-tricks/) (again from Cormac) where you can find some resolutions to other error codes.

# Conclusion

It was a **great learning experience and I discovered a lot of tools that can make our life easier** so hopefully the same applies to You.

Stay tuned for more :)