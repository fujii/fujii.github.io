---
layout: post
title: "Running WebKit on Windows"
description: ""
tags: 
---

Updated: 2022-02-14

I'm working on WebKit on Windows these days.
Sometimes I see someone asking about WebKit for Windows.
I would summarize the current situation.

## AppleWin port

[AppleWin port](https://trac.webkit.org/wiki/BuildingOnWindows) is an upstream port that is used for iTunes for Windows.
It uses the same components with Mac port, i.e. Core Graphics and Core Foundation, etc.

It supports only WebKit1.
[They removed AppleWin WebKit2](https://webkit.org/b/114096).
And, WebKit thread is not separated from UI thread.
The browser becomes unresponsive while running JavaScript benchmarks.

AppleWin port is supporting both 32bit and 64bit versions because iTunes for Windows is supporting them.
However, the 32bit version of Windows JavaScriptCore is supporting only Low-Level Interpreter C Loop, not supporting JIT.
LLInt CLoop has [the high stack consumption issue for MSVC](https://lists.webkit.org/pipermail/webkit-dev/2019-June/030718.html).

The latest 64bit built binary can be downloaded from Buildbot.
You need to install iTunes on your PC by using an MSI installer.
You can't use the Microsoft Store version of iTunes.

* <https://www.apple.com/itunes/download/win64>
* [How to run the latest WebKit ( Safari ) on Windows](https://medium.com/@alSkachkov/how-to-load-the-latest-webkit-on-windows-962a9219c1e1)
* <https://bugs.webkit.org/show_bug.cgi?id=206350>

AppleWin [has disabled](https://bugs.webkit.org/show_bug.cgi?id=222576) WebInspectorUI as default.
WebInspector can't be used with the binaries.

You can compile out both 32bit and 64bit AppleWin port by yourself.
However, the required library WebKitSupportLibrary.zip is [not redistributable](https://developer.apple.com/opensource/internet/webkit_sptlib_agree.html).

## WinCairo port

[WinCairo port](https://trac.webkit.org/wiki/BuildingCairoOnWindows) is a redistributable upstream Windows port.
It supports both WebKit1 and WebKit2, but it [plans to remove WebKit1](https://bugs.webkit.org/show_bug.cgi?id=194904).

It supports only 64bit.
Its JavaScriptCore supports LLInt Asm, baseline JIT, and DFG JIT, but neither FTL JIT nor Web Assembly.

The latest built binary can be downloaded from Buildbot.

* [Download build artifacts from Buildbot](https://trac.webkit.org/wiki/BuildingCairoOnWindows#DownloadbuildartifactsfromBuildbot)

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Latest <a href="https://twitter.com/hashtag/WebKit?src=hash&amp;ref_src=twsrc%5Etfw">#WebKit</a> MiniBrowser for Windows based on WinCairo 64<a href="https://t.co/SpdGSAsvW3">https://t.co/SpdGSAsvW3</a> :<br><br>Todo: `Download build artifacts from Buildbot` .<br>~ 125mb<br>I&#39;d like to embed this as WKwebView in Java .. but I can&#39;t. <a href="https://t.co/uCBnngXPK9">pic.twitter.com/uCBnngXPK9</a></p>&mdash; Friedhold Matz (@FriedholdMatz) <a href="https://twitter.com/FriedholdMatz/status/1275689950902734852?ref_src=twsrc%5Etfw">June 24, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

[Microsoft Playwright](https://www.npmjs.com/package/playwright) is distributing WinCairo port WebKit with some modifications.

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Playwright (WebKit on Windows) <a href="https://t.co/PCluWMkI2s">pic.twitter.com/PCluWMkI2s</a></p>&mdash; Fujii Hironori (藤井宏憲) (@fujii0) <a href="https://twitter.com/fujii0/status/1273354598921560065?ref_src=twsrc%5Etfw">June 17, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

Here is the list of Microsoft Playwright modifications to WinCairo as far as I know.

* Disabled high DPI support
* Disabled GPU process mode
* Use the complex text code path always
* Added a option to disable accelerated compositing (--disable-accelerated-compositing)

If you have installed [npm](https://www.npmjs.com/), you can run Playwright by the following command.

~~~
npx playwright wk https://webkit.org/
~~~

## QtWebKit

[QtWebKit](https://github.com/qtwebkit/qtwebkit) a downstream port that supports WebKit1 and WebKit2.

In WebKit1, WebKit thread and UI thread are separated.
It seems that Accelerated Compositing doesn't work on Windows.

* [Otter Browser](https://otter-browser.org/)

## WebKitGTK

WebKitGTK is a well maintained upstream port but doesn't support Windows.
So, Cygwin or WSL and X11 server (VcXsrv or Cygwin/X) are needed to run.
Cygwin has Epiphany and Midori packages, but very old.

You can use Microsoft Playwright on WSL.

~~~
wget https://playwright.azureedge.net/builds/webkit/1317/webkit-ubuntu-20.04.zip
unzip webkit-ubuntu-20.04.zip
apt-get install libnotify4 libenchant1c2a libgstreamer-plugins-bad1.0-0
./minibrowser-gtk/pw_run.sh https://google.com
~~~

See [browsers.json](https://github.com/microsoft/playwright/blob/master/browsers.json) and
[browserFetcher.ts](https://github.com/microsoft/playwright/blob/master/src/install/browserFetcher.ts) for the URL of the distribution.

Now, WebKitGTK and WPE WebKit have nightly build archives.
[Bug 215266 – \[GTK\]\[WPE\] Add a script for generating MiniBrowser bundles](https://bugs.webkit.org/show_bug.cgi?id=215266)

## WPE WebKit

WPE WebKit is a well maintained upstream port but doesn't support Windows.

## JavaFX WebKit

[Arunprasad Rajkumar](https://twitter.com/uint88) is [working on it in June 2019](https://lists.webkit.org/pipermail/webkit-dev/2019-June/030698.html).
But I don't know.

## MontionMark 1.1

I found [a StackOverflow question of WinCairo performance](https://stackoverflow.com/q/57610729).
Here are the benchmark results on my PC.

Browser                             | MontionMark 1.1
------------------------------------|-------------------
AppleWin r249040 (32bit) AC         | 8.39
AppleWin r249040 (32bit) non-AC     | 8.68
WinCairo r249042 (64bit) WK2 non-AC | 136.66
WinCairo r249042 (64bit) WK1 AC     | 68.99
QtWebKit (64bit) WK1 non-AC         | 115.18
Firefox 68.0.2 (64bit)              | 272.57
Firefox 68.0.2 (64bit) no-GPU       | 105.12

* Windows 10 version 1903 with 150% device scale factor display
* AppleWin: https://s3-us-west-2.amazonaws.com/archives.webkit.org/win-x86_64-release/249040.zip (This is a 32bit version regardless of the URL)
* WinCairo: https://s3-us-west-2.amazonaws.com/archives.webkit.org/wincairo-x86_64-release/249042.zip
* QtWebKit: otter-browser-win64-1.0.81-weekly272-setup.exe with manually adjusting zooming level

Here are the [screenshots](https://ibb.co/album/mwxUdv).
