---
layout: post
title: ThinkPad Edge E145 に Ubuntu 14.04 LTS 日本語 Remix をインストール
description: ""
category: 
tags: [linux, e145]
---

2月に[Ubuntu 13.10をインストール]({% post_url 2014-02-18-install-ubuntu-13-10-to-thinkpad-edge-e145 %})したばかりだが、
新しい Ubuntu で素の状態での動作具合が気になったので
Ubuntu 14.04 LTS 日本語 Remix を ThinkPad Edge E145 にインストールしました。

[ここ](https://www.ubuntulinux.jp/News/ubuntu1404-ja-remix) から ubuntu-ja-14.04-desktop-amd64.iso を取得しました。

[Installation/FromUSBStick - Community Help Wiki](https://help.ubuntu.com/community/Installation/FromUSBStick)
を参考に ubuntu-ja-14.04-desktop-amd64.iso と  usb-creator-gtk で
USB メモリにインストール CD を入れた。

起動時に F12 を押して BIOS の boot menu を出して後は問題なくインストール完了しました。

ひと通り試して気づいた問題は
キーボードの輝度ボタンで UI は表示されるが、バックライトの輝度は変化しないことのみでした。

バックライトは /etc/default/grub に以下のように指定しすれば解決します。

~~~
GRUB_CMDLINE_LINUX_DEFAULT="acpi_backlight=vendor"
~~~
