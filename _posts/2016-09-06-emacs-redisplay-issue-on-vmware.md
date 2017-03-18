---
layout: post
title: "Emacs redraw issue in X.org on VMWare and VirtualBox"
category : 
tags : [emacs, linux]
---
{% include JB/setup %}

## Issue

Emacs redraws sometimes partially while scrolling in X.org on VMWare or VirtualBox.
I tested only on Windows host OS.

Following error messages are output in `/var/log/Xorg.0.log` when this problem occurs.

~~~
[  1278.046] (EE) vmwgfx_scanout_update: failed to send dirty (-22, Invalid argument)
~~~

There are many reports of this problem:

* [Super User: ubuntu - Emacs does not redraw properly inside VirtualBox](http://superuser.com/questions/702494/)
* [Oracle VM VirtualBox: #13687 (Emacs redraw issues with >1 Virtualbox CPU)](https://www.virtualbox.org/ticket/13687)

I confirmed it happens even in one CPU configuration.

Vim seems to have the similar problems:

* [Stack Overflow: linux - Gvim redraw issues with Virtual Box and Windows 7 host](http://stackoverflow.com/questions/25018843/)
* [Debian Bug report: #777567 - vim-gtk: Regions of text fail to redraw in gvim](https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=777567)
* [Super User: vim - gvim redraw failure](http://superuser.com/questions/343599/)

## Workarounds

There are several workarounds I tried.

### Use Cairo

Emacs 25 supports Cairo by `configure --with-cario`.
This workaround does not hide the problem completely.
This makes it less frequently.

### Synchronous mode 

      emacs -xrm "emacs.synchronous: true"

This makes Emacs somewhat slow.

### Call redraw-display after scroll

~~~elisp
(when (eq window-system 'x)
  (add-hook 'window-scroll-functions (lambda (&rest x) (run-with-idle-timer 0.5 nil 'redraw-display))))
~~~

### Use compiz

Using compiz solves the problem.
Unfortunately, compiz makes painting slow.

### Use Xephyr

Xephyr also solve the problem.
On the other hand, Xnest doesn't help.

### Use X.Org fbdev driver

`/etc/X11/xorg.conf`:

~~~
Section "Device"
	Identifier "card 0"
	Driver "fbdev"
EndSection
~~~

### Switch wallpaper images every seconds

This triggers repainting whole scrren every seconds.

### Switch to other window and back to Emacs

Press Alt-Tab and Alt-Tab to switch window to repaint.



## Investigation

libX11 API calls can be observed by `ltrace`:

    ltrace -l libX11\* emacs

The following API are called frequently during scroll.

    emacs->XDrawImageString(0x306e800, 0x28000b0, 0x3b47ee0, 264) = 0
    emacs->XSetClipMask(0x306e800, 0x3b47ee0, 0, 0)             = 1
    emacs->XSetClipRectangles(0x306e800, 0x3b47ee0, 0, 0)       = 1
    emacs->XDrawImageString(0x306e800, 0x28000b0, 0x3b47ee0, 296) = 0
    emacs->XSetClipMask(0x306e800, 0x3b47ee0, 0, 0)             = 1
    emacs->XSetClipRectangles(0x306e800, 0x3b47ee0, 0, 0)       = 1


I created a test program and filed [a bug report](https://bugs.freedesktop.org/show_bug.cgi?id=97836).


## Apply a patch and compile the driver

I attached [my patch](https://bugs.freedesktop.org/show_bug.cgi?id=97836#c2).

~~~sh
dnf download --source xorg-x11-drv-vmware
sudo dnf builddep xorg-x11-drv-vmware
aunpack xorg-x11-drv-vmware-13.0.2-11.20150211git8f0cf7c.fc24.src.rpm
aunpack xorg-x11-drv-vmware-13.0.2-11.20150211git8f0cf7c.fc24.src/xf86-video-vmware-20150211.tar.bz2
cd xf86-video-vmware-20150211
patch -p0 ~/tmp/vmwgfx-dirty-union.patch
autoreconf -isv
./configure --prefix=$HOME/opt
make 
make install 
~~~
Create `/etc/X11/xorg.conf` with the following content.

~~~
Section "Files"
    ModulePath "/home/fujii/opt/lib/xorg/modules,/usr/lib64/xorg/modules"
EndSection
~~~
Log out and log in (lightdm restarts X server).

See the log `/var/log/Xorg.0.log` to check your driver is loaded.
