---
title: First steps with Pivotal Container Service (PKS)
date: 2018-06-19 07:58:40 +0000
tags:
- NSX-T
- Pivotal
- PKS
categories:
- Kubernetes
- Containers
description: Tips and Tricks I discovered using Pivotal Container Services (PKS)
cover: https://raw.githubusercontent.com/FabioChiodini/blog.kiodo.it/master/images/PKSCover.png
draft: false

---
I like Kubernetes and Containers so I set out to install Pivotal container service (PKS). 

I followed some good blog posts so I am going to provide link to them and add some commands and tricks that I found useful (just go to the paragraph marked as **my field notes**.

# What's PKS?

Pivotal Container Service (PKS) is a purpose-built product that enables enterprises and service providers to simplify the deployment and operations of Kubernetes clusters. It provides a production-grade Kubernetes distribution with deep VMwareNSX-T integration for advanced networking, a built-in private registry with enterprise security features and full life cycle management support of the clusters.

# What do You need?

Get some coffee and have a VMware environment available. I used my lab: a few ESXi hosts manager by vCenter. Go here for the versions:

[https://docs.pivotal.io/runtimes/pks/1-0/#prerequisites](https://docs.pivotal.io/runtimes/pks/1-0/#prerequisites "PKS Prerequisites")

# Installation Overview

If you want to leverage the full power of PKS (ie all integrations) thsi is the high level install process:

* Fix prerequisistes
* Install NSX-T for managing K8S network constructs and add-ons
* Prepare NSX-T constructs for PKS
* Install Ops Manager and BOSH
* Install PKS
* Deploy a Kubernetes Cluster
* Install Harbor to manage container images
* Install VMware vRealize Log Insight (vRLI) to get the logs from your environment
* Install VMware vRealize Operations Manager (vROps) to monitor your infrastructure

# Resources I have used

Here are the blog posts that helped me doing a _manual_ setup of PKS (it can be automated, that will be for another blog post):

[https://www.virtuallyghetto.com/2018/03/getting-started-with-vmware-pivotal-container-service-pks-part-1-overview.html](https://www.virtuallyghetto.com/2018/03/getting-started-with-vmware-pivotal-container-service-pks-part-1-overview.html "William Lam Blog")

As this blog post may appear daunting you may start reading a smaller version provided by the always amazing Cormac Hogan:

[https://cormachogan.com/2018/04/24/a-simple-pivotal-container-service-pks-deployment/](https://cormachogan.com/2018/04/24/a-simple-pivotal-container-service-pks-deployment/ "Faster setup")

## My field notes

# Installing NSX-T

The installation instructions for this this have been well-documented by my good friends at VMware so you can refer to the previous posts.

I especially recommend this if you are totally new to NSX-T:

[https://cormachogan.com/2018/04/11/building-a-simple-esxi-host-overlay-network-with-nsx-t/](https://cormachogan.com/2018/04/11/building-a-simple-esxi-host-overlay-network-with-nsx-t/ "NSX-T simpler")

## My field notes

I have largely simplified the setup provide by  Mr Lam as my 

# Troubleshooting BOSH

I was also on the lookout for bugs in the module and most of the bugs seem to be related with it since it's a bit outdated now.
I then activated the `virtualenv` and started entering the installation commands according to the instructions in the repository. Everything went on smoothly until the `python setup.py develop` command. I got an error as shown in the below picture.

![develop failed](https://raw.githubusercontent.com/UtkarshVerma/blog/source/static/images/netjsonconfig/django2.png)

Clearly, the error suggests that **Django v2.0.1** was being downloaded which **isn't supported** by **Python 2.7**. A bit of browsing led me to the conclusion that the `setup.py` needed to be modified to download **older Django versions** for Python 2.7. So, I added a simple `if-else` block to the django installation statement as shown in the picture.

![setup.py fix](https://raw.githubusercontent.com/UtkarshVerma/blog/source/static/images/netjsonconfig/my-fix.png)

If you're curious about my fix,  this is the [link](https://github.com/UtkarshVerma/django-netjsonconfig/commit/1575acbbc719e539cd8ecbffc761d8b9c2023d56).

Here, what's being done is basically:

* Detect `python` version used for installation using `sys.version_info\\\[0\\\]`.
* Install older Django versions if `sys.version_info\\\[0\\\]<3`, that is `python2` is detected.
* Install latest version if above condition isn't satisfied, that is `python3` is detected.

I also had to remove the django installation line from `requirements.txt` since `setup.py` was fetching the requirement names from there. After applying this fix, I re-ran the `python setup.py develop` command and there it was! The sweet smell of success. Now an **older yet python2 compatible** version of **Django** was being installed when using `python2` as clearly shown in one of the pictures below:

![Older Django being downloaded](https://raw.githubusercontent.com/UtkarshVerma/blog/source/static/images/netjsonconfig/django-v-fixed.png)

![Successful!](https://raw.githubusercontent.com/UtkarshVerma/blog/source/static/images/netjsonconfig/Success!.png)

After this, I installed some more requirements using `pip install -r requirements-test.txt`
This was how I'd finished installing `django-netjsonconfig` using Python2. Now all that was left to do was to to do the migrations and run the server.

# Making Migrations and Creating a superuser

By referring to the instructions on the repo again, I opened the `tests` directory,
did the migrations using `./manage.py migrate`. It was really satisfying to see all the CLI responses coloured in **green**. :smile:
![Migrate](https://raw.githubusercontent.com/UtkarshVerma/blog/source/static/images/netjsonconfig/migrate.png)

After that, there was the superuser creation using `./manage.py createsuperuser`.

![Superuser creation](https://raw.githubusercontent.com/UtkarshVerma/blog/source/static/images/netjsonconfig/superuser.png)

# Running the Test Server

I started the test server using `./manage.py runserver` and it was successful.

![Server Up and Running!](https://raw.githubusercontent.com/UtkarshVerma/blog/source/static/images/netjsonconfig/up-and-running.png)

I could also now visit the server at [http://localhost:8000/admin](http://localhost/admin).

![Logged In](https://raw.githubusercontent.com/UtkarshVerma/blog/source/static/images/netjsonconfig/logged-in.png)

# Conclusion

It was a great learning experience so hopefully the same applies to You. Stay tuned for more :)