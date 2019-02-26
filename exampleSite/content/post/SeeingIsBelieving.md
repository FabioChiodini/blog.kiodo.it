---
title: Seeing is Believing
date: 2019-02-06 03:29:43 +0000
tags:
- PKS
categories:
- VMware
description: Some field notes on how to monitor your Kubernetes cluster with VMware
  vRealize Operations
cover: https://raw.githubusercontent.com/FabioChiodini/blog.kiodo.it/master/images/DogEye.jpg

---
Thet title may sound philosophical but **You really need visibility in Your Kubernetes environment** especially if you're a n00b :P

# What's going on in my Kubernetes on VMware environment?

You may skip this if you want just the tech bytes :P

Checking the performances from the vCenter UI was always tricky and mostly a gentle art (I still remember the days of watching esxtop to understand what was going on) but we've moved on luckily!!

![](/uploads/esxtop2.jpg)

In this post I'll describe how to **set up monitoring in roughly 15 minutes for your Kubernetes clusters**

# What are the possible choices

If You google for "Kubernetes Monitoring" you'll find an insane number of options but most of them will not be very deep on what is happening at the infrastructure layer.

**As a recovering VMware admin I want to know which of my VM that is taking Kubernetes workloads is not performing well** so I went straight to vROPs ;)

Installing **vRealize Operations 7.0** is **as easy as deploying an OVA** and then just configure username and password. I am going to use the Small setup VM (2 vCPU, 16 GB of ram) and I have configured it to get data from the vCenter hosting my Pivotal Container Service (PKS) lab:

![](/uploads/vc-flyconfig.png)

\[This took just a few clicks and a few minutes, mostly waiting for the OVA deploy\]

Now on to configure it for the Kubernetes part!!

## Installing the plugin

I am going to use vRealize Operations 7.0 and the **latest plugin available** because I like fresh stuff (and the installation has changed recently) ;)

So let's download it from the [website ](https://marketplace.vmware.com/vsx/solutions/vrealize-operations-management-pack-for-container-monitoring?ref=related)(you need to sign up to download it). make sure you are using the latest one (1.2).

To add it to vROPs just **go to administration and click on Add**:

![](/uploads/adminsolution.png)

Now you need to deploy a **cAdvisor DaemonSet** in the cluster that you want to monitor.

**No worries if You don't know what it is**: in essence it's a pod that will start on every Kubernetes worker that you have and will be able to fetch data to feed to vROPs.

To deploy it have a look at my file [here](https://github.com/FabioChiodini/kiodo-pks-test/blob/master/vrops/vrops-cadvisor.yaml).

My only customization is that **I am using an image that comes straight from my Harbor container registry**.

So to deploy it just do:

    kubectl apply -f vrops-cadvisor.yaml

On your cluster.

To see if it is running:

    kubectl get daemonset --all-namespaces

You should see something like this (the **fluent-bit one is specific for PKS and has nothing to do with vROPs**, we'll talk about it next time but in essence does great stuff collecting your K8s and apps logs :P ):

![](/uploads/kubeds.png)

You should be set.

Just **check with a**

    kubectl describe daemonset vrops-cadvisor --namespace=kube-system

That the daemon set is **running on the right port** (or the one that You chose)

![](/uploads/kubeds2.png)

\[In this screenshot you can see **the port on which it is running** and if it's in a deployed status\]

Ok the **CLI part is DONE, let's switch back to the GUI** :P

## Let's configure it

Now click on the **gearbox** and then edit the configuration:

![](/uploads/geark8.png)

Now we will provide the **credentials to get to our K8s cluster** to vROPS:

![](/uploads/gearconfig.png)

Seems like a lot of stuff but let me break it down for you ;)

For the K8s name **it's super easy if you have PKS** you can just do this:

    pks clusters
    
    pkscluster yourclustername

get the ip and perform an **nslookup** to check for the right name:

![](/uploads/PKSCLI2.png)

Add an **https:// in the front** and a **:8443 in the back** and you're good

Now select **daemonSet** in the next field

Input the **port** (you have that via the kubectl describe command that we used before). In my case this is the default 31194

**Now for the login to the K8s cluster.**

Click on the green plus sign.

A popup will open, select **Token Auth** and use admin as credential name.

![](/uploads/tokenpks.png)

Now go to your **kube config file** an extract the token suaully this should require a:

    vi ~/.kube/config

The text that You want is just **after the id-token: part**

![](/uploads/tokenscreen.png)

Click on Ok, you may need to **accept the certificate** (that's a good sign!!):

![](/uploads/certificateacce.png)

and use the **test connection button**

![](/uploads/testconn.png)

Now you **just need some patience** as the collector will fetch the right data.

Go to d**ashboard -> Kubernetes dashboard and wait**

Here are **some screenshots for your viewing pleasure** (see how the info builds up nicely over time):

![](/uploads/vRops Install february 2019---11.png)

![](/uploads/vRops Install february 2019---12.png)

![](/uploads/vRops Install february 2019---13.png)

![](/uploads/vRops Install february 2019---14.png)

![](/uploads/vRops Install february 2019---15.png)

![](/uploads/vRops Install february 2019---16.png)

It totally seems that I **have nodes that are too small, we will fix them at the end of the blogpost ;)**

![](/uploads/vRops Install february 2019---17.png)

![](/uploads/vRops Install february 2019---19.png)

![](/uploads/vRops Install february 2019---20.png)

## **Next steps**

I totally plan to test more monitoring options but this is saving my day: I have already discovered that my nodes may be a little bit small.

Long story short **I'll resize them automatically by using BOSH** (as I am using PKS that makes my K8s-life easier):

I just have to go to **OpsManager, select my plan**:

![](/uploads/BOSHFTW.png)

And then **pick one of the many options** (bigger than the current one):

![](/uploads/Choicsemany.png)

As soon as I apply my changes the **automation will kick in and install fresh new nodes for my cluster gracefully removing the small ones** ;)

## Update 26 Feb 2019

There's a **new plugin available on the marketplace** (version 1.2.1-12305387):

https://marketplace.vmware.com/vsx/solutions/vrealize-operations-management-pack-for-container-monitoring#resources

The procedure for installing and configuring it does not change:

![](/uploads/vropsnewpack.png)

![](/uploads/vrops new pack-3.png)

## Closing Notes

Feel free to reach out to [me ](@FabioChiodini)with **your** feedback!!

F.