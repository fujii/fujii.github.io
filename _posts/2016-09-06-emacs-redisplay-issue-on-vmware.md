---
layout: post
title: "Emacs redraw issue in X.org on VMWare and VirtualBox"
category : 
tags : [emacs]
---
{% include JB/setup %}

## Issue

Many reports of this problem:

* [Super User: ubuntu - Emacs does not redraw properly inside VirtualBox](http://superuser.com/questions/702494/)
* [Oracle VM VirtualBox: #13687 (Emacs redraw issues with >1 Virtualbox CPU)](https://www.virtualbox.org/ticket/13687)

* [Stack Overflow: linux - Gvim redraw issues with Virtual Box and Windows 7 host](http://stackoverflow.com/questions/25018843/)
* [Debian Bug report: #777567 - vim-gtk: Regions of text fail to redraw in gvim](https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=777567)
* [Super User: vim - gvim redraw failure](http://superuser.com/questions/343599/)

## Workarounds

* Use compiz
* Use Xephyr
* Use X.Org fbdev driver

`/etc/X11/xorg.conf`:

~~~
Section "Device"
	Identifier "card 0"
	Driver "fbdev"
EndSection
~~~

* Switch wallpaper images every seconds
* Call redraw-display after scroll

~~~elisp
(when (eq window-system 'x)
  (add-hook 'window-scroll-functions (lambda (&rest x) (run-with-idle-timer 0.5 nil 'redraw-display))))
~~~

* Alt-Tab Alt-Tab
* Synchronous mode 

      emacs -xrm "emacs.synchronous: true"

## Note

libX11 API calls can be observed by `ltrace`:

    ltrace -l libX11\* emacs

The following API are called frequently during scroll.

    emacs->XDrawImageString(0x306e800, 0x28000b0, 0x3b47ee0, 264) = 0
    emacs->XSetClipMask(0x306e800, 0x3b47ee0, 0, 0)             = 1
    emacs->XSetClipRectangles(0x306e800, 0x3b47ee0, 0, 0)       = 1
    emacs->XDrawImageString(0x306e800, 0x28000b0, 0x3b47ee0, 296) = 0
    emacs->XSetClipMask(0x306e800, 0x3b47ee0, 0, 0)             = 1
    emacs->XSetClipRectangles(0x306e800, 0x3b47ee0, 0, 0)       = 1
