---
layout: post
title: "ThinkPad Edge E145 Backlight Brightness Issue"
description: ""
category: 
tags: [e145, linux]
---
{% include JB/setup %}

The backlight brightness can not be adjusted with backlight hotkeys
while a indicator will be shown on the top left conner of screen.
When I plug in and out a power cable, the backlight brightness is getting brighter and darker.

# standard ACPI backlight interface

Standard ACPI backlight interface is in /sys/class/backlight/acpi_video0 directory.
But, this does not work.

~~~
$ echo 50 | sudo tee /sys/class/backlight/acpi_video0/brightness
~~~

This interface can be disabled by kernel option in /etc/default/grub.

~~~
GRUB_CMDLINE_LINUX_DEFAULT="acpi_backlight=vendor"
~~~

# fglrx

My video card can be checked with lspci command:

~~~
$ lspci | grep Radeon
00:01.0 VGA compatible controller: Advanced Micro Devices, Inc. [AMD/ATI] Kabini [Radeon HD 8240]
~~~

This page <http://www.x.org/wiki/Projects/Drivers/> lists X.Org video drivers.
Radeon has following choices:

* fglrx
* radeon
* fbdev
* vesa

The driver being used can be checked with xorg's log:

~~~
$ grep -i "\bconnected" /var/log/Xorg.0.log
[    75.225] (II) fglrx(0): Connected Display0: LVDS
[    75.230] (II) fglrx(0): Output LVDS connected
[    75.231] (II) fglrx(0): Adapter AMD Radeon HD 8240 has 2 configurable heads and 1 displays connected.
~~~
fglrx is used.

The X.org video driver fglrx requires a kernel driver fglrx, which has the same name.
The kernel driver fglrx can be checked with lsmod command:

~~~
$ lsmod | grep fglrx
fglrx                8081247  53 
amd_iommu_v2           19227  1 fglrx
~~~
The version of loaded fglrx kernel driver can be checked with dmesg command:

~~~
$ dmesg | grep "fglrx.*module loaded"
[   19.306266] <6>[fglrx] module loaded - fglrx 13.35.5 [Jan 29 2014] with 1 minors
~~~

Unfortunately the kernel driver fglrx does not exposes a interface under /sys/class/backlight directory.

Catalyst Control Center (amdcccle) has a setting of PowerPlay which controls
backlight brightness in case of plugging power cabel in and out.

<iframe src="https://www.flickr.com/photos/117734135@N07/12868368764/player/938125d85b" height="410" width="500"  frameborder="0" allowfullscreen webkitallowfullscreen mozallowfullscreen oallowfullscreen msallowfullscreen></iframe>

This mode can be switched by aticonfig command.
The aticonfig will fail if /etc/X11/xorg.conf file does not exist.
The command ```sudo aticonfig --initial``` should be invoked as the first time.

I created a script based on /usr/share/doc/fglrx/examples/etc/acpi/ati-powermode.sh.

~~~bash
#!/bin/bash 

low=false
has_pplib=false

if [[ "$1" == -l ]]; then
  low=true
fi

# If PPLIB is enabled
/usr/bin/aticonfig --pplib-cmd="get version" | grep -q PPLIB
if [[ $? == 0 ]]; then
   has_pplib=true
fi

if $low; then
    echo "Low power"
    if $has_pplib; then
        /usr/bin/aticonfig --pplib-cmd="notify psrc dc"
    else
        /usr/bin/aticonfig --set-powerstate=1 --effective=now
    fi
else
    echo "high power"
    if $has_pplib; then
        /usr/bin/aticonfig --pplib-cmd="notify psrc ac"
    else
        /usr/bin/aticonfig --set-powerstate=3 --effective=now
    fi
fi
~~~
This script can turn backligh on and off, while this is not a ideal solution.


# radeon

Unfortunately, a video driver radeon does not support Kabini (Radeon HD 8240) at the moment.
On the other hand, the same name kernel driver radeon can handle backlight.
The kernel driver radeon is blacklisted by fglrx.
To use radeon, fglrx should be uninstalled.

~~~
$ sudo apt-get remove fglrx\*

~~~
The radeon kernel driver will be loaded after rebooting.
The interface under /sys/class/backlight/radeon_bl0 directory works fine.

~~~
$ echo 50 | sudo tee /sys/class/backlight/radeon_bl0/brightness
50
$ echo 255 | sudo tee /sys/class/backlight/radeon_bl0/brightness
255
~~~

X.org selects vesa as the default video driver.
This does not work.
Specify fbdev in /etc/X11/xorg.conf.

~~~
Section "Device"
	Identifier  "Card1"
	Driver      "fbdev"
	BusID       "PCI:0:1:0"
EndSection
~~~

# thinkpad_acpi

The kernel driver thinkpad_acpi is another backlight driver.

To clarify problem, I blacklisted fglrx and radeon.

~~~
$ sudo tee /etc/modprobe.d/blacklist-fbdev.conf <<EOF
blacklist fglrx
blacklist radeon
EOF
~~~

If the standard ACPI backlight interface is available,
thinkpad_acpi disables its backlight control.
Edit kernel option to disable it.

~~~
GRUB_CMDLINE_LINUX_DEFAULT="acpi_backlight=vendor"
~~~
Invoke sudo update-grub and reboot.
thinkpad_acpi reports following unsupported message.

~~~
$ dmesg | grep thinkpad
[   13.271147] thinkpad_acpi: ThinkPad ACPI Extras v0.25
[   13.271157] thinkpad_acpi: http://ibm-acpi.sf.net/
[   13.271161] thinkpad_acpi: ThinkPad BIOS HSET56WW (2.01 ), EC unknown
[   13.271165] thinkpad_acpi: Lenovo ThinkPad Edge E145, model 20BCCTO1WW
[   13.272291] thinkpad_acpi: Unsupported brightness interface, please contact ibm-acpi-devel@lists.sourceforge.net
[   13.282550] thinkpad_acpi: radio switch found; radios are enabled
[   13.282586] thinkpad_acpi: possible tablet mode switch found; ThinkPad in laptop mode
[   13.294500] thinkpad_acpi: Console audio control enabled, mode: monitor (read only)
[   13.300765] input: ThinkPad Extra Buttons as /devices/platform/thinkpad_acpi/input/input13

~~~

Added a kernel option acpi_osi=Linux.

~~~
GRUB_CMDLINE_LINUX_DEFAULT="acpi_osi=Linux acpi_backlight=vendor"
~~~
And, reboot.

~~~
$ dmesg | grep thinkpad
[   11.975891] thinkpad_acpi: ThinkPad ACPI Extras v0.25
[   11.975898] thinkpad_acpi: http://ibm-acpi.sf.net/
[   11.975902] thinkpad_acpi: ThinkPad BIOS HSET56WW (2.01 ), EC unknown
[   11.975905] thinkpad_acpi: Lenovo ThinkPad Edge E145, model 20BCCTO1WW
[   12.003505] thinkpad_acpi: detected a 16-level brightness capable ThinkPad
[   12.012390] thinkpad_acpi: radio switch found; radios are enabled
[   12.012424] thinkpad_acpi: possible tablet mode switch found; ThinkPad in laptop mode
[   12.098052] thinkpad_acpi: Standard ACPI backlight interface not available, thinkpad_acpi native brightness control enabled
[   12.100469] thinkpad_acpi: Console audio control enabled, mode: monitor (read only)
[   12.134546] input: ThinkPad Extra Buttons as /devices/platform/thinkpad_acpi/input/input11
~~~
This message looks good.
But, interface under /sys/class/backlight/thinkpad_screen directory does not work expectedly.
