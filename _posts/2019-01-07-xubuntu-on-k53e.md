---
layout: post
title: "ASUS K53E に Xubuntu をインストール"
description: ""
category: 
tags: 
---
{% include JB/setup %}

7年前に婚約者(現妻)と同棲するときに彼女の古い PC を捨てて、二人で折半して買った思い出のノート PC である。
別に私の PC があったが、それには Windows をインストールしないため、Windows PC が必要だった。

最近はたいていのことはスマホで済むため、あまり使用されていない。
現在の主な用途は奥さんが Google Sheets で家計簿を入力すること。
他には年に一度くらいの頻度で発生する Excel 需要 (PTA の名簿が Excel ファイルとか)で、OneDrive で編集するときや、なにか Windows アプリを使う必要が発生したときとか。

ほぼブラウザが使えればどうにかなる。
たいていの一般家庭はもう PC はほぼ使ってないだろうし、もはや PC を必要とすることは、そのことが問題である。
名簿ごときで Excel ファイルでやり取りするのは、みんな困ってるのじゃないだろうか？

* OS: Windows 10
* Display: 15.6 インチ WXGA (1366x768)
* CPU: Core i5 2410M 2.3GHz/2コア
* RAM: 4GB
* Graphics: Intel HD Graphics 3000
* HDD: 640GB 

* 2011/10月 購入 ￥46,256
* 2017/3月 交換用バッテリー購入

最近ではときたま裏で何らかの処理が走って数秒ほど固まるようになって、これがストレスになってきた。
Windows PC としてはスペック的には買い換えたほうがいいのだが、たいして使用してないのと、
Android タブレットなんかとくらべると十分なスペックにも思えるので Linux で延命できるか試してみる。

この20年、Linux のインストールは何度もしてきたが、今回は奥様の PC である。
20年前の Linux desktop を知っていると、ずいぶんと便利になったと思うが、一般人に薦めるレベルには到達はしていないと思う。

私が普段使っているので Xubuntu を選んだ。
XFCE 風味 (flavor) の Ubuntu である。
XFCE はユーザ=開発者といった雰囲気で、私にとっては必要な機能はほぼ100%備わっているのだが、
足りない部分は Unix コマンドで設定できることが前提なので初心者にはおすすめしない。
半年ごとのリリースでは保守が面倒なので、２年毎の Xubuntu LTS 18.04 にした。

普通じゃない方法でインストールしようとしたため、ややハマる。Linux は難しい。
Ubuntu の mini.iso で Xubuntu Minimal をインストールしようとしたら、インストーラーが途中で止まらなくなった。
適当に選択項目を変更して再チャレンジで成功した。

https://help.ubuntu.com/community/Installation/MinimalCD

Linux desktop を日本語で使うはトラブルのもとなのだが、今回は入れざるをえない。
インストールを英語でやったら、インストール後に XFCE で日本語を追加する方法が存在しなかった。
Gnome 用の language-selector-gnome をインストールした。
XFCE はこんな感じで開発者が必要としないあれこれが足りてない。

Xubuntu にすると固まることはなくなって、快適である。
気になるのは Chrome でスクロールしているときの描画が汚い。
移行した直後なので気になるが、しばらくしたら忘れてしまいそう。
概して Linux の device driver は Windows のものよりも低品質なので、そのためだろうか。

あと Windows キーでメニューが開かない。
[解決方法](https://qiita.com/kazoo04/items/6fb6eeb16e1213c1c15b)を見つけたが、これだと Windows key + 文字キー が変なことになる。
デフォルトで Windows キー + W で Web Browser が起動するので、Windows key + 文字キー を足していくのが無難か。
Windows キー + R でローンチャーっぽいのが表示されるが、なんか使いづらい。
Alt + F2 のもなんか使いづらい。
結局は Windows キー + T でターミナルを開いてコマンド叩くのが XFCE 流なのだろうか。

トラックパッドの設定は２本指スクロールを選択しているはずなのに、なぜかエッジスクロールと２本指スクロールの両方が有効になっている。
トラックパッドのエッジに触れるとスクロールしてしまうためキーボード入力中に誤操作が頻発する。

他にも elementary OS や TDE, Budgie Desktop も気になる。
* [elementary OS](https://elementary.io/)
* [妻のパソコンを Windows 7 から Windows 10 にアップグレードしたら使いにくいとキレられたので Linux にした件](https://eng-blog.iij.ad.jp/archives/2430)
* [Budgie Desktop](https://github.com/solus-project/budgie-desktop)

それと、Chromium OS や Android-x86 も気になる。
いつか試したい。
