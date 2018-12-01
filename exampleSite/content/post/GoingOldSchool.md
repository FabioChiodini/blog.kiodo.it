---
title: Going Old School - BIOS and other things
date: 2018-12-01 04:29:43 +0100
tags:
- PKS
categories:
- VMware
description: Some field notes on upgrading BIOS and going below the value line
cover: https://raw.githubusercontent.com/FabioChiodini/blog.kiodo.it/master/images/BIOS.jpg

---
Saturday morning doing expenses so why not fix a few things in the Lab :P

# The Value line

You may skip this if you want just the tech bytes :P

At pivotal we have this note concepts of the value line (more details [HERE ](https://content.pivotal.io/blog/automated-ops-freedom-to-innovate-part-2)): it is a fairly simple concept. At some point in time you have to decide at which layer of your stack you'll stop messing up and have somebody did that for You.

![](/uploads/1 ciCUoa6LTD80qD0MEw1FfQ.jpeg)

Unfortunately in my lab I have to do everything and I cannot buy a good thing that would update BIOS and firmware on his own like a good [VxRail](https://content.pivotal.io/blog/automated-ops-freedom-to-innovate-part-2)

# Upgrading BIOS to save the Lab

My lab relies on a few servers that are getting old (mainly R610 and R710) and vSphere 6.7 does not **officially** (see this [workaround ](https://www.thehumblelab.com/vsphere-67-homelabs-unsupported-cpu/))so I went out to ebay to buy some X56xx processors and embarked in the hard work of making hardware work properly ;)

Long story short upgrading BIOS is not always trivial so here are some good notes

## Updating with Dell Boot CD

This is fairly easy, just boot up your Dell servers with an ISO image downloaded from [here ](https://www.dell.com/support/article/it/it/itbsdt1/sln296511/updating-dell-poweredge-servers-via-bootable-media-iso?lang=en)and let him do the dirty job :P

## The special one ;)

One of these servers was an Avamar node (repurposed hardware coming from my fine friends at DellEMC so flashing his BIOS was not trivial as the normal procedures did not work

After googling around for a while I managed to perform the upgrade.

I had to prepare a boot USB with FreeDOS ([HERE ](https://pingtool.org/bootable-dos-iso-bios-upgrade/)for the full procedure) and copy over the files.

![](/uploads/BiosR710-3.png)

Next I used the USB image to boot via DRAC

![](/uploads/BiosUpgradeR710.png)

And then I used this command to force the upgrade:

/forcetype

As this was BIOS 1.1.x I went to 3.x an then up to 6.x

Feel free to reach out to [me ](@FabioChiodini)with **your** tricks!!

F.