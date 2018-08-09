---
title: 'More automation for Pivotal Container Service? '
date: 2018-07-09 02:29:43 +0000
tags: []
categories:
- Kubernetes
- DevOps
- Containers
description: Some scripts to speed up Pivotal Container Service install/reinstall
cover: https://raw.githubusercontent.com/FabioChiodini/blog.kiodo.it/master/images/automate.jpeg

---
VMworld Sessions

If you are installing Pivotal Container Service® (PKS) more than once or if you need more automation for test environment **you can have it with PKS :)**

AS most of the interface is API based you just have to use some CLI abilities ;)

In my case I scavenged around for good scripts and I collected them [in one github repo](https://github.com/FabioChiodini/pks_scripts).

Here are some **notes on how to install the prerequisites** for the scripts.

# Powershell

if you are installing on Windows 2012 R2 [this a great guide](http://thesolving.com/virtualization/how-to-install-and-configure-vmware-powercli-version-10/).

BTW what I always forget is to allow all scripts

    Set-ExecutionPolicy RemoteSigned

Remember to **execute this as Administrator**

More paragraphs and links to come!!
