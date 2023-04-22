+++ 
date = 2023-04-14T20:41:45+02:00
title = "Proxmox VE Full Desktop Setup"
description = "On this post, I wiil be sharing my optimal Setup for Proxmox VE with a Desktop Environment"
slug = "proxmoxsetup"
authors = ["Ramo Molise"]
tags = ["Proxmox"]
categories = ["Setup"]
externalLink = ""
series = []
+++

# Step 1: Install Proxmox & Configure Logical Volumes

* First install Proxmox in a Boot Drive

By default, Proxmox creates two Logical Volumes, I would recommend using all your disk space in a single Logical Volume especially if you are planning to use your Proxmox as a full Desktop with a user interface and GUI applications, or if you have a small drive.

* To use the full Boot Drive as a single logical volume, increase the "local" volume storage size by first deleting the "local-lvm" volume on the Proxmox user Interface. Once removed, resize the "local" volume on the Shell using these commands:

```
lvremove /dev/pve/data
lvresize -l +100%FREE /dev/pve/root
resize2fs /dev/mapper/pve-root`
```

# Step 2: Additional Storage (Optional)
* Add additional HDD's/SSD's by Wiping each Drive and Initializing it to GPT under **System>Disks**. After Wiping and Initializing the Disks, under **System>Directory**, add each Disk by creating new Directory, selecting the Disk, then naming the Disk.

*Note: By adding your drives as Proxmox Storage, the Desktop Environment wont be able to pick up the drives as a normal PC would, but you will have more storage options for your VM's. For those planning on having a NAS, rather do a full disk passthrough.*

# Step 3: Install User Interface

You can use any method you prefer to install any **Desktop Environment** or **Window Manager** through the command line, here is a simple method using **tasksel** which comes preinstalled on Proxmox:

* Install Desktop Environment using Tasksel:

```
apt update && apt upgrade
tasksel
```

You will be given options to install some of the common Desktop Environments, select what you prefer.

# Step 4: Add User Account

* Add new User Account

```
adduser username
```

* Add new User to sudo group

```
/usr/sbin/usermod -aG sudo username
```

 * Reboot the sytem

```
reboot now
```

*Note: I've come accross an issue when trying to login with the new user and the login manager just reloads and does nothing. If you are facing this, login with root user then under your Desktop Environments Settings, delete the previously created user, and recreate the user. Logout and then login again using your non-root user.*

# Step 5: Optional Settings

When using SPICE as a Display for your Virtual Machines, install Virt Viewer.

* Install Virt Viewer
```
sudo apt install virt-viewer
```

* On the Proxmox User Interface, click on the your Virtual Machine, enable all Spice Enhancements under **Options**
* Spice also has Guest tools/drivers on  https://www.spice-space.org/download.html for both Windows and Linux

I find that Nomachine is best for my use cases as a Remote-Desktop Service for my VM's as I can fully use my VM's at 60FPS including audio over WiFi in my local network. Nomachine even has an app for my old Android 4.4 Tablet thus I can even run Windows 11 indirectly through the tablet

* Download Nomachine .deb file https://www.nomachine.com/ and install with:

```
dpkg -i nomachine*.deb
```

# My Experience Gaming Directly on Proxmox VE Desktop

My experience directly gaming on Proxmox VE is very decent on my Ryzen 7 5700G with Vega 8, a majority of my Steam Games work perfectly using the latest Proton, Proton Experimental and/or Proton GE with the exceptionally annoying problem of not running Warframe.

Warframe launches and complains about DirectX10 being unsupported, so I thought maybe updating the drivers would make a difference but when I try to to install amdgpu firmware, Proxmox will not install the amdgpu firmware without removing the pve firmware, which I'm not willing to do.

GPU passthrough should work sufficiently for it though. In all honestly, the fact that I can even play certain Steam games directly on Proxmox was a huge surprise. Proxmox is really powerful, and extremely versatile. Do give it a shot!
