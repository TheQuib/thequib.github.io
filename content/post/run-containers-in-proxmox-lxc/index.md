---
author: "Quinn Henry"
title: "Run Docker Containers in Proxmox LXC"
date: "2022-06-26"
description: "Quick guide on getting Docker to run in a Proxmox LXC."
tags: ["homelab", "containerization"]
categories: ["Docker", "Proxmox", "Virtualization"]
image: featuredImage.png
---

## 🙋 Why would I do this?

One awesome feature in Proxmox VE is to run an LXC (Linux Container) directly from the web interface. A quick machine can be spun up that works and acts just like a regular full-blown Linux Virtual machine.

The downside to this is we are limited to what resources you provide the VM, not the resources that are given to an LXC (which is essentially the maximum of the host system).


## ➡️ Side Note

This requires running an LXC as `Unprivileged`. To learn more about what this means, please refer to the [Proxmox documentation](https://pve.proxmox.com/wiki/Unprivileged_LXC_containers) on this.


## ⌨️ Preparing Proxmox

***Note:*** *This will need to be done on each Proxmox host you have, so each command will need to be done for how ever many hosts you have*


### Enable Kernel Modules

The `overlay` and `aufs` kernel modules need to be enabled to support running containers:

```bash
echo -e "overlay\naufs" >> /etc/modules-load.d/modules.conf
```

 - Please note that the `aufs` module is deprecated as of Proxmox VE 7.0
   - This CAN be explicitly enabled, but it won't do anything


### Reboot host

For the changes to take effect, reboot the host


### Check changes

Upon starting back up, run the follwing:

```bash
lsmod | grep -E 'overlay|aufs'
```

This should return something like:

```sh
overlay               131072  3
```


## Create an unprivileged container

The creation process is pretty standard, and what you're used to. But there are a few changes you will need to make along the way:

 - On the Proxmox GUI, click the `Create CT` button

![](createCTButton.png)

 - Make sure you have "Unprivileged container" checked
 - For the rest of the setup, give it your preferred configuration
 - Make sure you don't start the container upon creation


## Edit container's features

Click on your new container
 - Go to `Options`
 - Double-click on the `Features` row
 - Ensure that the following are checked:
   - `keyctl`
   - `nesting`
 - Click `OK`
 - Start the container


## Install Docker

Head over to [Docker's docs](https://docs.docker.com/engine/install/), and find the steps to install docker for your distro.

Once all set up, test out the installation with `docker run hello-world` and it should be working!