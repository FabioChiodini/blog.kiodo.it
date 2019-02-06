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

Installing vRealize Operations 7.0 is as easy as deploying an OVA and then just configure username and password. I am going to use the Small setup VM (2 vCPU, 16 GB of ram) and I have configured it to get data from the vCenter hosting my Pivotal Container Service (PKS) lab:

![](/uploads/vc-flyconfig.png)

Now on to configure it for the Kubernetes part!!

## Installing the plugin

I am going to use vRealize Operations 7.0 and the latest plugin available because I like fresh stuff (and the installation has changed recently) ;)

So let's download it from the [website ](https://marketplace.vmware.com/vsx/solutions/vrealize-operations-management-pack-for-container-monitoring?ref=related)(you need to sign up to download it). make sure you are using the latest one (1.2).

To add it to vROPs just go to administration and click on Add:

![](/uploads/adminsolution.png)

Now you need to deploy a cAdvisor DaemonSet in the cluster that you want to monitor.

No worries if You don't know what it is: in essence it's a pod that will set on every Kubernetes worker that you have and will be able to fetch data to feed to vROPs.

To deploy it have a look at my file [here](https://github.com/FabioChiodini/kiodo-pks-test/blob/master/vrops/vrops-cadvisor.yaml).

My only customization is that i am using an image that comes straight from my harbor container registry.

So to deploy it just do:

    kubectl apply -f vrops-cadvisor.yaml

On your cluster.

To see if it is running:

    kubectl get daemonset --all-namespaces

You should see something like this (the fluent-bit one is specific for PKS, we'll talk about it next time but in essence does great stuff collecting your K8s and apps logs :P ):

![](/uploads/kubeds.png)

You should be set.

Just check with a 

    kubectl describe daemonset vrops-cadvisor --namespace=kube-system

That the daemon set is running on the right port (or the one that You chose)

![](/uploads/kubeds2.png)

Ok the CLI part is DONE, let's switch back to the GUI :P

## Let's configure it

Now click on the gearbox and then edit the configuration:

![](/uploads/geark8.png)

Now we will provide the credentials to get to our K8s cluster to vROPS:

![](/uploads/gearconfig.png)

Seems like a lot of stuff but let me break it down for you ;)

For the K8s name if you have PKS you can just do this:

pks clusters

pkscluster yourclustername

get the ip and perform an nslookup to check for the right name

Add an https:// in the front and a :8443 in the back and you're good

Now select daemonSet in the next field

Input the port (you have that via the kubectl describe command that we used before)

## **Next steps**

I totally plan to test more monitoring options but this is saving my day 

## Closing Notes

Feel free to reach out to [me ](@FabioChiodini)with **your** feedback!!

F.