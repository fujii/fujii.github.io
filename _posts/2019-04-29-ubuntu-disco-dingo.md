---
layout: post
title: "Ubuntu 19.04"
description: ""
tags: 
---

GNOME 3.32 で Fractional Scaling Support された。

* [Treviño's Blog » Archives » Resource Scale for Fractional Scaling support in GNOME Shell 3.32](https://blog.3v1n0.net/informatica/linux/gnome-shell-fractional-scaling-in-wayland-landed/)
* [GNOME 3.32 Lands Long-Awaited Fractional Scaling Support - Phoronix](https://www.phoronix.com/scan.php?page=news_item&px=GNOME-3.32-Fractional-Scaling)

期待して Ubuntu 19.04 を試したものの、
Fractional Scaling Support はテキストがぼやける。
これでは使えない。
アイコン画像はスケールしていいが、テキストは dot-by-dot で描画が不可欠。
そもそも、[gtk_widget_get_scale_factor の返り値が整数](https://developer.gnome.org/gtk4/stable/GtkWidget.html#gtk-widget-get-scale-factor) の時点で
厳しいのかもしれない。
gnome-tweak-tool で text scale をいじって文字だけスケールできるので、アイコンは小さいけど、我慢できる。

それと、描画が高速化されたらしい。

* [More GNOME Performance Optimizations Being Tackled Thanks To Canonical - Phoronix](https://www.phoronix.com/scan.php?page=news_item&px=More-GNOME-Performance-January)
* [GNOME Shell Gets a Major Speed Boost - OMG! Ubuntu!](https://www.omgubuntu.co.uk/2019/01/gnome-shell-performance-fixes-coming)

期待していたものの、Gnome は相変わらず重い。

で結局いつもの Xubuntu に切り替えた。
