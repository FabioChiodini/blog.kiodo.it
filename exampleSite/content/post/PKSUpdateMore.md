---
title: Updating PKS to 1.1.x More tricks
date: 2018-09-22 04:29:43 +0200
tags: []
categories:
- Kubernetes
- DevOps
- Containers
description: Some field notes on upgrading to PKS 1.1.x and NSX-T
cover: https://raw.githubusercontent.com/FabioChiodini/blog.kiodo.it/master/images/certificates-certificates-certificates.jpg

---
Saturday afternoon after a feverish (literally) week so let's log some new stuff to the blog ;)

# Kiodo and Certificates

You may skip this if you want just the tech bytes :P

My love/hate relationship with certificates dates back to Windows Server 2003 times (yes I'm that old :P)

Long story short a "Consultant" was sent to the financial institution where I was a sysadmin. He was the "expert" that was sent to install our Certification authority. He showed up with a W2K3 handbook printed on many lose A4 pages and 20 minutes into the discussion he stated that we had to install the main CA on one of our DCs. That ended the meeting. He was never to be spotted again in our office :P

Now off to certificates in 2018 and how they can still be tricky :P

# Upgrading Pivotal Container Service to 1.1.5 and NSX-T certificates

A quick blog post to hopefully help when you are updating PKS from 1.0.x to 1.1.x ;)

I've recently spent more time with Customers using Pivotal Container Service® (PKS) so here are some good notes from the field.

If you are using NSX-T there a a couple of things that you need to check.

First thing you'll need **NSX-T 2.1 or 2.2** (at the time of writing it is better to upgrade to 1.1.5 that supports [both ](https://docs.pivotal.io/runtimes/pks/1-1/release-notes.html#v1.1.5);))

As usual release notes are your friends so here's an handy link https://docs.pivotal.io/runtimes/pks/1-1/release-notes.html

An important change

**From PKS 1.1.x you have to configure NSX-T in the Director** tile:

There are some corner cases where **your update may _seem_ to work even if you do not configure this** so make sure that you do this after configuring the certificates and superuser objects in NSX (as described in here).

When configuring this you may encounter this error:

I found this when using an NSX instance using a self signed certicate.

Examining my cert I found out this:

So the certificate seems to be malformed.

The easy way to fix this was to use this script provided by my good friend Brice (a real ninja when it comes to PKS ;)

The script not only creates the superuser and related certificates but it also generates a new cert for the NSX Manager (ie the one above) with a proper FQDN

Again usual disclaimer: do not DO this in production environments (I assume you have proper certs there!! ;) )

Feel free to reach out to [me ](@FabioChiodini)with **your** tricks!!

F.