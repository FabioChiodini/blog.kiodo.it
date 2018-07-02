---
title: Updating Pivotal Container Service (PKS)
date: 2018-07-02 09:58:40 +0200
tags:
- Update
- BOSH
- Pivotal
- PKS
categories:
- Kubernetes
- Containers
description: Tips and Tricks I discovered updateing Pivotal Container Services (PKS)
cover: https://raw.githubusercontent.com/FabioChiodini/blog.kiodo.it/master/images/Update.jpg

---
Time to update PKS!!

[PKS 1.1 just shipped](https://content.pivotal.io/blog/pivotal-container-service-1-1-now-ga-helps-you-run-kubernetes-without-complexity-why-pks-just-works) so time to upgrade the Lab

# What's PKS?

Pivotal Container Service (PKS) is a purpose-built product that enables enterprises and service providers to simplify the deployment and operations of Kubernetes clusters. It provides a production-grade Kubernetes distribution with deep VMware NSX-T integration for advanced networking, a built-in private registry with enterprise security features and full life cycle management support of the clusters.

It is not just "yet another Kubernetes distribution" but it provides more a "Kubernetes as service experience.

What do I mean by that? Using an API (PKS API) you can just ask for a Kubernetes Cluster and PKS will provision one for You. Not only that but it will let you manage/upgrade it in Day 1 and 2 and also integrate it with the right networking/monitoring/logging/security add-ons that you need in a production environment.

If you want more details just go [here](https://content.pivotal.io/blog/secure-multitenant-kubernetes-in-minutes-pivotal-container-service-goes-ga "HERE") or [here ](https://www.youtube.com/watch?v=bQKra0CB5zE)if you like videos better :)

# What do You need?

A Running PKS installation.

Here's my setup:

If you want some notes on how to set this up here they are:

[Setting up PKS Blog post](https://gifted-raman-00870e.netlify.com/post/pksbasics/ "Setting up PKS Blog post")

# Upgrade Overview

Here are the high level steps:

* Download new version of PKS from [Pivotal Network](https://network.pivotal.io/ "Pivotal Network")
* Review release notes!! :P
* Upgrade using Pivotal Ops Manager
* Upgrade your Kubernetes Clusters

# Resources I have used

The official guides from Pivotal are always helpful:

[https://docs.pivotal.io/runtimes/pks/1-0/upgrade-pks.html](https://docs.pivotal.io/runtimes/pks/1-0/upgrade-pks.html "https://docs.pivotal.io/runtimes/pks/1-0/upgrade-pks.html")

## My field notes

# Conclusion

Stay tuned for more :)