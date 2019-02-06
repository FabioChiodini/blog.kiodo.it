---
title: Seeing is Believing
date: 2018-12-01 03:29:43 +0000
tags:
- PKS
categories:
- VMware
description: Some field notes on how to monitor your Kubernetes cluster with VMware
  vRealize Operations
cover: https://raw.githubusercontent.com/FabioChiodini/blog.kiodo.it/master/images/BIOS.jpg

---
It may sound philosophical but You need visibility in Your Kubernetes environment  especially if you're a n00b :P

# What's going on?

You may skip this if you want just the tech bytes :P

Checking the performances from the vCenter UI was always tricky and mostly a gentle art (I still remember the days of watching esxtop to understand what was going on) but we've moved on luckily!!

# What are the possible choices

If You google for "Kubernetes Monitoring" you'll find an insane number of options but most of them will not be very deep on what is happening at the infrastructure layer.

As a recovering VMware admin i want to know which of my VM that is taking Kubernetes workloads is not performing well so I went straight to 

Installing vRealize Operations 7.0 is as easy as deploying an OVA and then just configure username and password. I am going to use the Small setup VM (2 vCPU, 16 GB of ram) and I have configured it to get data from the vCenter hosting my Pivotal container service (PKS) lab

## Installing the plugin

I am going to use vRealize Operations 7.0 and the latest plugin available because I like fresh stuff (and the installation has changed recently) ;)

So let's download it from the website (you need to sign up to download it)

## Let's configure it

## **Next steps**

I totally plan to test more monitoring options but this is saving my day 

## Closing Notes

Feel free to reach out to [me ](@FabioChiodini)with **your** feedback!!

F.