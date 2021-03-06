---
title: Updating PKS to 1.1.x More tricks
date: 2018-09-22 04:29:43 +0200
tags:
- PKS
categories:
- Kubernetes
- Containers
- Pivotal
- VMware
description: Some field notes on upgrading to PKS 1.1.x and NSX-T
cover: https://raw.githubusercontent.com/FabioChiodini/blog.kiodo.it/master/images/certificates-certificates-certificates.jpg

---
Saturday afternoon after a feverish (literally!!) week so let's log some new stuff to the blog ;)

# Kiodo and Certificates

You may skip this if you want just the tech bytes :P

My love/hate relationship with certificates dates back to Windows Server 2003 times (yes I'm that old :P)

Long story short a "Consultant" was sent to the financial institution where I was a sysadmin. He was the "expert" that was sent to install our Certification authority.

He showed up with a W2K3 handbook printed on many lose A4 pages and 20 minutes into the discussion he stated that we had to install the main CA on one of our DCs. That ended the meeting.

He was never to be spotted again in our office.

I know it sounds a lot BOFH-ish style but it's what happened :P

Now off to certificates in 2018 and how they can still be tricky ;)

# Upgrading Pivotal Container Service to 1.1.x and NSX-T certificates

A quick blog post to hopefully help when you are updating PKS from 1.0.x to 1.1.x ;)

I've recently spent more time with Customers using Pivotal Container Service® (PKS) so here are some good notes from the field.

If you are using NSX-T there a a couple of things that you need to check.

First thing you'll need **NSX-T 2.1 or 2.2** (at the time of writing it is better to upgrade to the latest version 1.1.5 that supports [both ](https://docs.pivotal.io/runtimes/pks/1-1/release-notes.html#v1.1.5);))

As usual release notes are your friends so here's an handy link https://docs.pivotal.io/runtimes/pks/1-1/release-notes.html

## Clean-up Before the upgrade

If you are upgrading from 1.0.x and you "experimented" a lot you may have some stale data in your NSX-T setup.

You could use this [script](https://github.com/bdereims/pks-prep/blob/master/nsx-t/99-cleanup.sh) to clean up the ip pools.

Some **important notes BEFORE using this script**:

* You can do this  cleanup only if you have no PKS deployed clusters left.
* Always make sure you're backing up your NSX-T install frequently ;)

This super tool is provided by my good friend [Brice ](https://twitter.com/bdereims)(a real ninja when it comes to PKS ;) ).

To use it populate the variables in the script and execute it AT YOUR OWN RISK.

![](/uploads/CleanIPPools.png)

![](/uploads/CleanIPPools-2.png)

This will free up some ips for your next setup.

## An important change

**From PKS 1.1.x you have to configure NSX-T in the Director** tile (in 1.0.x you had to configure it to "Standard vCenter networking"):

![](/uploads/DirectorNSXT.png)

![](/uploads/DirectorNSXT-2.png)

There are some corner cases where **your update may _seem_ to work even if you do not configure this** so make sure that you do this after configuring the certificates and superuser objects in NSX (as described in here).

When configuring Ops Manager this you may encounter this error:

"Error connecting to NSX IP: The NSX server's certificate is not signed with the provided NSX CA cert. Please provide the correct NSX CA cert"

![](/uploads/ErrorNSXTCertificate.png)

I found this when using an NSX-T instance using a self signed certificate.

Examining my certificate I found out this (i just had an hostname and not FQDN or ip in my cert):

![](/uploads/NSX-T certicate API-2.png)

So the certificate seems to be malformed.

The easy way to fix this was to use this [script ](https://github.com/bdereims/pks-prep/blob/master/nsx-t/4-nsx-cert.sh)provided by my good friend [Brice ](https://twitter.com/bdereims)(a real ninja when it comes to PKS ;) ):

The script not only creates the superuser and related certificates but it also generates a new cert for the NSX Manager (ie the one above) with a proper FQDN.

Again usual disclaimer: **do not DO this in production environments** (I assume you have proper certs there!! ;) )

## A few notes on using the script

The script uses some environment variables that must be populated in an env file that must be present in the root of the git folder tree that you downloaded.

Just copy the env-example file (in a file named env) and then edit it.

![](/uploads/envBrice.png)

Also make sure that the two variables at the beginning of the script have valid values.

Feel free to reach out to [me ](@FabioChiodini)with **your** tricks!!

F.