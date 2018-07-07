---
title: Updating Pivotal Container Service (PKS)
date: 2018-07-02 07:58:40 +0000
tags:
- Update
- BOSH
- Pivotal
- PKS
categories:
- Kubernetes
- Containers
description: Tips and Tricks I discovered updating Pivotal Container Services (PKS)
cover: https://raw.githubusercontent.com/FabioChiodini/blog.kiodo.it/master/images/Update.jpg

---
Time to update Pivotal Container Service (PKS)!!

In this blog post I'll provide you with **some notes on the upgrade process** and **why I performed the upgrade**.

[PKS 1.1.0 just shipped ](https://content.pivotal.io/blog/pivotal-container-service-1-1-now-ga-helps-you-run-kubernetes-without-complexity-why-pks-just-works)so time to upgrade the Lab (read the next paragraph for the bullet-point-version of the new features) :)

**MY USUAL DISCLAIMER:** These steps are not meant for a production environment, always refer to official support and documentation for this type of setups.

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

If you want some notes on how to set PKS (1.0.4) up here they are:

[Setting up PKS Blog post](https://gifted-raman-00870e.netlify.com/post/pksbasics/ "Setting up PKS Blog post")

# Upgrade Overview

Here are the high level steps:

* **Download** new version of PKS from [Pivotal Network](https://network.pivotal.io/ "Pivotal Network")
* Review [the release notes](https://docs.pivotal.io/runtimes/pks/1-1/release-notes.html)!! :P
* **Upgrade Harbor** (as you've read the release notes)
* Setup **new authentication method for NSX-T**
* **Upgrade PKS** using Pivotal Ops Manager
* **Upgrade your Kubernetes Clusters**

# Resources I have used

The official guides from Pivotal are always helpful:

[https://docs.pivotal.io/runtimes/pks/1-0/upgrade-pks.html](https://docs.pivotal.io/runtimes/pks/1-0/upgrade-pks.html "https://docs.pivotal.io/runtimes/pks/1-0/upgrade-pks.html")

## My field notes

## Pre Update

Starting by checking that everything is up and running:

A good old **_bosh vms_** command could be a good start:

![](/uploads/PKSPreUpdate-2.png)

It is also a good idea to check the version deployed to recheck them after the update. Use **_bosh deployments_** to get the versions installed:

![](/uploads/PKSPreUpdate-3.png)

## NSXT-T Authentication

With this version user/password authentication for NSX-T seems to be gone :O

Time for some command line!!

Refer to step 6 [in this guide](https://docs.pivotal.io/runtimes/pks/1-1/installing-nsx-t.html) for the full instructions.

Or if you like quick lab notes go [here](https://www.definit.co.uk/2018/06/upgrading-pks-with-nsx-t-from-1-0-x-to-1-1/)

In essence you have to set up a Super User Principal Identity that will authenticate to NSX-T using a certificate.

## NSX-T restore

You may find this process useful (as I did) :P

If you make some big mistakes with the scripts you may find yourself with an "unclean" configuration of NSX-T.

But if you have [scheduled some backups](http://pubs.vmware.com/nsxt-11/index.jsp?topic=%2Fcom.vmware.nsxt.admin.doc%2FGUID-E6181BF1-2CB7-4870-B508-BFAF5B47D702.html) you can still be fine ;)

The restore process is quite simple:

* Shut down old NSX-T Manager
* Deploy from ova a new one with the same name, ip, login and other settings
* Log in to the new one
* Restore the backup from your backup repository

![](/uploads/NSX-T restore.png)

![](/uploads/NSX-T restore-2.png)

## Update steps

PKS 1.1 requires also a stemcell upgrade (requires 3586.24) so after you uploaded the PKS package you'll also have to update the stemcell:

![](/uploads/PKSUpdate-2.png)

Download it from [here](https://network.pivotal.io/products/stemcells#/releases/121367)

And then configure PKS to use it:

![](/uploads/PKSUpdate-3.png)

Now you are ready to configure a couple of things in PKS before starting the update:

When it comes to understanding the new ip block I suggest you to refer to this article:

[https://docs.pivotal.io/runtimes/pks/1-1/installing-nsx-t.html](https://docs.pivotal.io/runtimes/pks/1-1/installing-nsx-t.html)

For the sake of simplicity I've kept the same block nodes for now in My Lab:

![](/uploads/PKSUpdate-5.png)

Remember to populate a DNS address for the pods.

But.. if you read the release notes (I didn't!! :P) you would have known that you also have to **upgrade Harbor as you need version 1.5 for PKS 1.1, if you hadn't you'll get this error**

> _Cannot generate manifest for product VMware Harbor Registry: Error in (( .properties.auth_mode.selected_option.parsed_manifest(uaa) )): Error in https://(( ..pivotal-container-service.properties.uaa_url.value )):8443: unknown property "uaa_url" (Product "VMware Harbor Registry" / Job: nil) (Product "VMware Harbor Registry" / Job: nil)_

![](/uploads/PKSUpdate-8.png)

So download the 1.5 release of Harbor and update it via Ops Manager:

![](/uploads/PKSUpdate-9.png)

Click on "_Apply Changes"_ and let BOSH do its magic :)

### Adding an IP range for nodes

If you are adding an ip range for nodes and you have Harbor installed please note [this](https://docs.vmware.com/en/VMware-Pivotal-Container-Service/1.1/vmware-pks-11/GUID-PKS11-installing-nsx-t.html) ie:

Harbor uses the following IP blocks for its internal bridges:

* 172.17.0.1 /16
* 172.18.0.1 /16
* 172.19.0.1 /16
* 172.20.0.1 /16
* 172.21.0.1 /16
* 172.22.0.1 /16

I used 172.14.0.0/24 in my environment.

# Conclusion

Stay tuned for more :)