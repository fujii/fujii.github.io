---
layout: post
title: "Video driver performance issue of VirtualBox 6.0 and Xorg 1.19 and newer"
description: ""
category: 
tags: 
---

VirtualBox is Oracle's excellent open-source virtual machine product.
I was quite comfortable with it until version 6.0.

I knew a Reddit user has warned version 6.0 issues, but I upgraded.

* [PSA: Do NOT install VirtualBox 6! : virtualbox](https://www.reddit.com/r/virtualbox/comments/aaipnl/psa_do_not_install_virtualbox_6/)


## VirtualBox 6.0

I'm using Xubuntu 18.10 guest on Windows 10 host.
After upgrade, graphics performance became horrible.
It was able to resize the screen.
Xorg server process was always consuming CPU time.
Larger screen size, more CPU load.

`Xorg.0.log` shows failing to load `vboxvideo` Xorg driver.

> [    53.621] (EE) Failed to load module "vboxvideo" (module does not exist, 0)

It seems that `fb` sub module of `fbdev` module was loaded.
I guess this is the cause of CPU load.

Here is [the full log](https://gist.github.com/fujii/72f994019a2de9ca2532f387feab540f).

According to [this comment](https://forums.virtualbox.org/viewtopic.php?f=15&t=84201#p399506), `vboxvideo` Xorg driver is not needed.

> That's true but we don't intend to ship a driver for X.org 1.19 because systems with such a new X server should be able to load the vboxvideo kernel module.

According to [Oracle® VM VirtualBoxRelease Notes for Release 6.0: 1.8. User Interface Changes](https://docs.oracle.com/cd/E97728_01/F12470/html/gui-changes.html),
version 6.0 has three types of virtual video controllers, VBoxSVGA, VBoxVGA and VMSVGA.
VBoxSVGA is the default. 
I tried VMSVGA, too. `vmware` Xorg driver was loaded in that case.
It performed better than `fbdev`, but not good enough.


## VirtualBox 5.2

Then, I downgraded to version 5.2, and everything is working fine.

* `lsmod` shows `vboxvideo` and `vmwgfx` kernel modules are loaded.
* `lspci` shows `VMware SVGA II Adapter`.
* `Xorg.0.log` shows `vmware` Xorg driver is used.

Here is [the full log](https://gist.github.com/fujii/76ee6699f493c10729ba9658bdad3448).


## VirtualBox 6.0.12

In 2019-10-16, I tested VirtualBox 6.0.12 with Xubuntu 19.04.
The situation gets worse.

* VMSVGA:
    * It's easy to crash VirtualBox by closing chrome window
    * Seems [this issue](https://forums.virtualbox.org/viewtopic.php?f=6&t=94657).
    * bad performance while using OpenGL (chrome or glmark2)
* VBoxSVGA: bad performance

I found Oracle's blog article, and tested it with Ubuntu 18.04 too.
VMSVGA works nicely, but VBoxSVGA doesn't.
I didn't try installing xserver-xorg-video-vmware-hwe-18.04.

* [Oracle VM VirtualBox 6.0: 3D Acceleration for Ubuntu 18.04 Guest \| Simon Coter Blog](https://blogs.oracle.com/scoter/oracle-vm-virtualbox-6-3d-acceleration-for-ubuntu-1804-guest)
