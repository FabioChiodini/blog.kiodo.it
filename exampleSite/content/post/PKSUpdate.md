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
Time to update Pivotal Container Service (PKS)!! 

In this blog post I'll provide you with some notes on the upgrade process and why I performed the upgrade.

[PKS 1.10 just shipped ](https://content.pivotal.io/blog/pivotal-container-service-1-1-now-ga-helps-you-run-kubernetes-without-complexity-why-pks-just-works)so time to upgrade the Lab (read the next paragraph for the bullet-point-version of the new features) :)

# So why am I upgrading?

Upgrades are cool but they can be painful as we all know ;)

My main goal for upgrading is **understanding if PKS can make these upgrades easier.** 

But obviously I want to be able to enjoy the latest and greatest being shipped by version 1.10 so:

* Updated **Kubernetes** version **1.10.3**
* **Another layer of High Availability for your Kubernetes cluster**: PKS now supports availability zones (ie span your workers and masters (beta) installations across different clusters)
* **More multitenancy**: now you can automatically provision Kubernetes cluster along with a dedicated network (via NSX-T integration)
* Automatic cleanup of unused NSX-T constructs
* **Easier integration with VMware** vRealize Network Insight and VMware Wavefront
* Expanded [**LDAP support**](https://docs.pivotal.io/runtimes/pks/1-1/manage-users.html#cluster-access)

The full list of improvements is available [here](https://docs.pivotal.io/runtimes/pks/1-1/release-notes.html)

# What do You need?

A Running PKS installation: as easy as that :)

Here's my setup:

![](/uploads/PreUpgrade.png)

As you can see my plan is to upgrade a 1.0.4 installation to a 1.1

If you want some notes on how to set PKS up here they are:

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

Starting by checking that everything is up and running:

A good old **_bosh vms_** command could be a good start:

![](/uploads/PKSPreUpdate-2.png)

# Conclusion

Stay tuned for more :)