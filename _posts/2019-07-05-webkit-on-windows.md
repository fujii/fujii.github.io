---
layout: post
title: "Running WebKit on Windows in 2019"
description: ""
tags: 
---

I'm working on WebKit on Windows these days.
Sometimes I see someone asking about WebKit on Windows.
I would summarize the current situation.

* [Is there anyone working on bringing WebKit2 to Windows?](https://www.reddit.com/r/WebKit/comments/a4zu94/is_there_anyone_working_on_bringing_webkit2_to/)


## AppleWin port

[AppleWin port](https://trac.webkit.org/wiki/BuildingOnWindows) is a upstream port.
It uses the same components with Mac port, Core Graphics and Core Foundation, and etc.
But, WebKitSupportLibrary.zip is [not redistributable](https://developer.apple.com/opensource/internet/webkit_sptlib_agree.html).

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
It supports both WebKit1 and WebKit2, but it [plans to remove WebKit1](https://bugs.webkit.org/show_bug.cgi?id=194904).
WinCairo WebKit2 doesn't support Accelerated Compositing at the moment.

It supports only 64bit.
Its JavaScriptCore supports LLInt Asm, baseline JIT and DFG JIT, but neither FTL JIT nor Web Assembly.

The latest built binary can be downloaded from Buildbot.

* [Download build artifacts from Buildbot](https://trac.webkit.org/wiki/BuildingCairoOnWindows#DownloadbuildartifactsfromBuildbot)

## QtWebKit

[QtWebKit](https://github.com/qtwebkit/qtwebkit) a downstream port which supports WebKit1 and WebKit2.

In WebKit1, WebKit thread and UI thread are separated.
It seems that Accelerated Compositing doesn't work on Windows.

* [Otter Browser](https://otter-browser.org/)

## WebKitGTK

WebKitGTK is a well maintained upstream port, but doesn't support Windows.
So, Cygwin and Cygwin/X are needed to run.
Cygwin has epiphany and midori packages, but very old.


## WPE WebKit

WPE WebKit is a well maintained upstream port, but doesn't support Windows.

## JavaFX WebKit

[Arunprasad Rajkumar](https://twitter.com/uint88) is [working on it in June 2019](https://lists.webkit.org/pipermail/webkit-dev/2019-June/030698.html).
But I don't know.

## FTW port

[FTW port](https://bugs.webkit.org/show_bug.cgi?id=199206) is a new upstream Windows port which uses Direct2D and DirectDraw.

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
* AppleWin: https://s3-us-west-2.amazonaws.com/archives.webkit.org/wincairo-x86_64-release/249042.zip
* WinCairo: https://s3-us-west-2.amazonaws.com/archives.webkit.org/win-x86_64-release/249040.zip (This is a 32bit version with regardless to the URL)
* QtWebKit: otter-browser-win64-1.0.81-weekly272-setup.exe with manually adjusting zooming level

Here are [screenshots](https://ibb.co/album/mwxUdv).
