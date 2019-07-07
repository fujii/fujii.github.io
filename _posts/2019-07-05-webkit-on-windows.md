---
layout: post
title: "Running WebKit on Windows in 2019"
description: ""
tags: 
---

I'm working on WebKit on Windows these days.
Sometimes I see someone asking about WebKit on Windows.

* [Is there anyone working on bringing WebKit2 to Windows?](https://www.reddit.com/r/WebKit/comments/a4zu94/is_there_anyone_working_on_bringing_webkit2_to/)

I'd summarize the current situation.

## AppleWin port

[AppleWin port](https://trac.webkit.org/wiki/BuildingOnWindows) is a upstream port.
It uses the same components with Mac port, Core Graphics and Core Foundation, and etc.
But, WebKitSupportLibrary.zip is [not redistribute](https://developer.apple.com/opensource/internet/webkit_sptlib_agree.html).

It supports only WebKit1.
And, WebKit thread is not separated from UI thread.
The browser becomes unresponsive while running JavaScript benchmarks.

Although iTunes for Windows is using both 32bit and 64bit version of AppleWin port,
only 32bit version of WebKitSupportLibrary.zip and WebKitAuxiliaryLibrary.zip is publicly available at the moment.
So, you can compile only 32bit version.

The latest built binary can be downloaded from Buildbot. iTunes needs to be installed on your PC.

* [How to run the latest WebKit ( Safari ) on Windows](https://medium.com/@alSkachkov/how-to-load-the-latest-webkit-on-windows-962a9219c1e1)

32bit version of Windows JavaScriptCore is supporting only Low Level Interpreter C Loop, not supporting JIT.
LLInt CLoop has [the high stack consumption issue for MSVC](https://lists.webkit.org/pipermail/webkit-dev/2019-June/030718.html).
Theoretically, it's possible to use 64bit version of WebKit.dll included in iTunes.

## WinCairo port

[WinCairo port](https://trac.webkit.org/wiki/BuildingCairoOnWindows) is the only one redistributable upstream Windows port.
It supports both WebKit1 and WebKit2, but it plans to remove WebKit1.

* [Bug 194904 – \[WinCairo\] Remove WebKitLegacy support](https://bugs.webkit.org/show_bug.cgi?id=194904)

It supports only 64bit.
Its JavaScriptCore supports LLInt Asm, baseline JIT and DFG JIT, but FTL JIT.

The latest built binary can be downloaded from Buildbot.

* [Download build artifacts from Buildbot](https://trac.webkit.org/wiki/BuildingCairoOnWindows#DownloadbuildartifactsfromBuildbot)

## QtWebKit

[QtWebKit](https://github.com/qtwebkit/qtwebkit) a downstream port which supports WebKit1 and WebKit2.

In WebKit1, WebKit thread and UI thread are separated.
It seems that Accelerated compositing doesn't work on Windows.

* [Otter Browser](https://otter-browser.org/)

## WebKitGTK

WebKitGTK is a well maintained upstream port, but doesn't support Windows.
So, Cygwin and Cygwin/X are needed to run.
Cygwin has epiphany and midori packages.

It seems that a env var `WEBKIT_DISABLE_COMPOSITING_MODE=1` needs to be set to disable accelerated compositing.


## JavaFX WebKit

[Arunprasad Rajkumar](https://twitter.com/uint88) is [working on it in June 2019](https://lists.webkit.org/pipermail/webkit-dev/2019-June/030698.html).
But I don't know.

## WPE WebKit

WPE WebKit is a well maintained upstream port, but doesn't support Windows.

## FTW port

[FTW port](https://bugs.webkit.org/show_bug.cgi?id=199206) is a new upstream Windows port which uses Direct2D.
