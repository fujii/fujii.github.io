---
layout: post
title: ThinkPad Edge E145 に Ubuntu 13.10 をインストール
description: ""
category: 
tags: [linux, e145]
---
{% include JB/setup %}

自宅の Linux PC が徐々に壊れ始めたので、
廉価版 ThinkPad である ThinkPad Edge の E145 を購入しました。

はじめての Windows 8 なので
しばらく試してみたかったが、
いつ完全に起動しなくなくなるかわからない状態で
遊んでる余裕がないのですぐに Linux をインストールしました。
Windows 8 はほとんど使ってないが、もっさりで耐え難いスペックのように感じました。

<iframe src="https://www.flickr.com/photos/117734135@N07/12560862614/player/d0587e64ca" height="375" width="500"  frameborder="0" allowfullscreen webkitallowfullscreen mozallowfullscreen oallowfullscreen msallowfullscreen></iframe>

[Installation/FromUSBStick - Community Help Wiki](https://help.ubuntu.com/community/Installation/FromUSBStick)
を参考に ubuntu-13.10-desktop-amd64.iso と  usb-creator-gtk で
USB メモリにインストール CD を入れた。

起動時に F12 を押しても BIOS の boot menu が出ない。
[Windows 8のシャットダウン、電源の切り方いろいろ](http://freesoft.tvbok.com/win8/windows8-shutdown.html)
の下の方法で解決しました。

> コントロールパネル  
> ＞「電源の管理」  
> ＞「電源ボタンの動作を選択」  
> 　＞「電源ボタンの定義とパスワード保護の有効化」  
> 　　＞「現在利用可能ではない設定を変更します」  
> 　　　＞「シャットダウン設定」  
> 　　　　＞「高速スタートアップを有効にする」を無効にする  

インストールは完了したが、
X server が起動しない。

[しょうちゃんの日記: ThinkPad Edge E145を購入した](http://tsyouji525.blogspot.jp/2013/12/thinkpad-edge-e145.html)
の情報をもとに fglrx-updates をインストールして再起動したら解決した。

数時間使用しての感想。
私にはキーボードのキーが硬いです。
全キー30g重 の Readforce のキーボード愛好者にはキーが硬く、もう指が痛いです。
Insert キーがない。 xterm で Shift-Insert でのペーストはよく使っているので、困る。
Fnキーと左Ctrlの入れ替えと
ファンクションキーは BIOS に設定があった。
デフォルトではファンクションキーは Fnキーと組み合わせが必要である。
たとえば、Alt+F4 を押したいときは Alt+Fn+F4に相当するキーの３つを押す必要がある。
PtrSc が何故か右 Alt と Ctrl の間に配置されている。
間違えてスクリーンキャプチャを撮ってしまう。

タッチパッドは Mac みたいに２本指でスクロールする。
タッチパッドはなぜか使いにくく感じます。

**2014-02-23 追記**  
Insert キーは Fn + I でした。
[ユーザガイド](http://support.lenovo.com/ja_JP/guides-and-manuals/detail.page?DocID=UM018768)
には書かれていないが、他のは記載されています。

> Fn + B: Break キーと同じ機能があります。  
> Fn + P: Pause キーと同じ機能があります。  
> Fn + S: SysRq キーと同じ機能があります。  
> Fn + K: ScrLK キーと同じ機能があります。  

マウスのミドルクリックのボタンがスペースキーのすぐ下にあるので
xterm はこれでもペースト出来ました。

さらに、IBus から Fcitx に変更したら Ctrl + ; でもペースト出来ました。
