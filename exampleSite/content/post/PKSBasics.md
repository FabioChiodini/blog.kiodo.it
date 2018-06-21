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
description: Tips and Tricks I discovered using Pivotal Container Services (PKS)
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

If you want to leverage the full power of PKS (ie all integrations) thsi is the high level install process:

* Fix prerequisistes
* Install NSX-T for managing K8S network constructs and add-ons
* Prepare NSX-T constructs for PKS
* Install Ops Manager and BOSH
* Install PKS
* Deploy a Kubernetes Cluster
* Install Harbor to manage container images
* Install VMware vRealize Log Insight (vRLI) to get the logs from your environment
* Install VMware vRealize Operations Manager (vROps) to monitor your infrastructure

# Resources I have used

Here are the blog posts that helped me doing a _manual_ setup of PKS (it can be automated, that will be for another blog post):

[https://www.virtuallyghetto.com/2018/03/getting-started-with-vmware-pivotal-container-service-pks-part-1-overview.html](https://www.virtuallyghetto.com/2018/03/getting-started-with-vmware-pivotal-container-service-pks-part-1-overview.html "William Lam Blog")

As this blog post may appear daunting you may start reading a smaller version provided by the always amazing Cormac Hogan:

[https://cormachogan.com/2018/04/24/a-simple-pivotal-container-service-pks-deployment/](https://cormachogan.com/2018/04/24/a-simple-pivotal-container-service-pks-deployment/ "Faster setup")

## My field notes

# Installing NSX-T

The installation instructions for this this have been well-documented by my good friends at VMware so you can refer to the previous posts.

I especially recommend this if you are totally new to NSX-T:

[https://cormachogan.com/2018/04/11/building-a-simple-esxi-host-overlay-network-with-nsx-t/](https://cormachogan.com/2018/04/11/building-a-simple-esxi-host-overlay-network-with-nsx-t/ "NSX-T simpler")

## My field notes

I have largely simplified the setup provide by  Mr Lam as my 

# Troubleshooting BOSH

# Conclusion

It was a great learning experience so hopefully the same applies to You. Stay tuned for more :)