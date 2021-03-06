---
layout: post
title: "WSL で Emacs 環境構築"
description: ""
category: 
tags: [linux, emacs]
---

巷で流行の WSL に移行しました。

## X server

VcXsrv を試したが、4つの不具合があって常用には厳しかった。

Emacs 起動直後の画面サイズが異常である。
RandR の問題?

* [WindowsでX-Server越しにEmacsを立ち上げると初期画面がおかしくなる件 - FPGA開発日記](http://msyksphinz.hatenablog.com/entry/2018/07/19/040000)
* [VcXsrv Windows X Server / Bugs / #44 Issues with](https://sourceforge.net/p/vcxsrv/bugs/44/)

Emacs 入力中に突然にキー入力できなくなる。
システムトレイのアイコンをクリックすると復活する。
一日に一度くらいの再現頻度。一度再現すると X を再起動するまで簡単に再現する。

* [VcXsrv Windows X Server / Bugs / #59 stuck keys in vcxsrv 1.19.5.2](https://sourceforge.net/p/vcxsrv/bugs/59/)

Emacs26 と double buffer extension (DBE) で何も表示されない。最新リリースでは改修されたようだ。

* [VcXsrv Windows X Server / Feature Requests / #32 DBE (double buffer extension) support](https://sourceforge.net/p/vcxsrv/feature-requests/32/)
* [x11 - Emacs 26 build on RHEL6 has blank window frame - Emacs Stack Exchange](https://emacs.stackexchange.com/questions/42323/emacs-26-build-on-rhel6-has-blank-window-frame)

コピペで一部の文字が欠落する。具体的には『–』。 Bugzilla が title にその文字を入れるために、ブラウザ拡張でタイトルをクリップボードへコピーする度に困る。例えば、

* [189044 – Add specialized template declarations of HashTraits and DefaultHash to detect misuse](https://bugs.webkit.org/show_bug.cgi?id=189044)

というわけで Cygwin/X を使ってます。
上記の問題はなさそうです。
気になっているは high DPI でマウスカーソルが小さいことくらい。
とは言っても xterm と emacs でマウスはほぼ使用していない。

Cygwin の xlaunch をインストールすれば、拡張子 .xlaunch の関連付けをしてくれる。
xlaunch で `-listen tcp` を指定して起動する。
[.xlaunch ファイル](https://gist.github.com/fujii/bb60dcfdb3dfb77a82f51f8aa5a751db#file-x-xlaunch)。

あとは適当なファイル名でシェルスクリプトを書いて wsl コマンドで実行すればいいだけだが、
コンソールが表示されてしまう。
コンソールを非表示にするには
XMing の [The Run utility](http://www.straightrunning.com/XmingNotes/#head-148) を使う方法と、
wscript の方法がある。 
後者を採用した。適当なファイル `start-wsl.js` を作成して wscript で実行する。

~~~js
var shell = new ActiveXObject("WScript.Shell");
shell.Run("wsl ~/.xstart", 0, true);
~~~

`~/.xstart` で初期化と xterm を起動する。

~~~sh
export SHELL=zsh
export DISPLAY=:0
export LIBGL_ALWAYS_INDIRECT=1
cd
xrdb ~/.Xresources
xterm -ls
~~~

## APT のミラーサーバの設定

* [repository - How can I get apt to use a mirror close to me, or choose a faster mirror? - Ask Ubuntu](https://askubuntu.com/a/37754)

[18.04 LTS (Bionic Beaver) 用 sources.list](https://gist.githubusercontent.com/fujii/547da696c67294955ab984da25c329ef/raw/bb778388611266878f42225e8b5fe1f38157ec38/sources.list) を /etc/apt/sources.list に置く。

もしくは software-properties-gtk で設定する。

## start コマンド

Windows の start や Cygwin の cygstart のようなコマンド。下のページのものを拝借した。

* [WSL で start コマンドを使う – HIGASHI Taiju – Medium](https://medium.com/@h.taiju/use-the-start-command-on-wsl-bea3d82725e1)

`wslu` というパッケージに `wslview` というコマンドが追加された。 `sudo apt install ubuntu-wsl` でインストールして置くといいみたい。

## Mozc の設定

Mozc の設定はコマンドで起動。

~~~sh
# 設定
/usr/lib/mozc/mozc_tool --mode=config_dialog
# 辞書ツール
/usr/lib/mozc/mozc_tool --mode=dictionary_tool
# 単語登録
/usr/lib/mozc/mozc_tool --mode=word_register_dialog
# 手書き文字入力
/usr/lib/mozc/mozc_tool --mode=hand_writing
# 文字パレット
/usr/lib/mozc/mozc_tool --mode=character_palette
~~~

## 感想

体感では Cygwin と同じくらい遅い。
ざっくり簡単にベンチマークすると Cygwin より WSL のほうがやや速いようではある。
下の続報に期待したい。

* [Microsoft、WSLのI/Oパフォーマンス改善に取り組み \| マイナビニュース](https://news.mynavi.jp/article/20180815-676572/)

Cygwin の emacs-w32 は不安定だったが、Emacs が安定して嬉しい。
M-x view-hello-file がいい感じに多言語表示されてる。

これで脱 Cygwin できるかと思ったが、
現状は Cygwin/X を使っているので完全にはできてない。

VcXsrv の問題の調査でやたらと時間がかかった。
ひたすら検索してただけだが。
Cygwin/X がこんなによくできているなら、
emacs-w32 を使わなくても emacs-x11 で良かったんじゃないかと思えたが、今更 Cygwin に戻る気はない。

はじめてみた wscript.exe のアイコンにはかるく衝撃を受けた。

![wscript.exe のアイコン]({{ BASE_PATH }}/assets/wsl-wscript-icon.png)

ConPTY がリリースされれば xterm はいらなくなりそうだし、VcXsrv のリリースも頻繁なので Cygwin/X も不要になるかもしれない。
WSL まわりの環境はしばらくは模索が続くだろう。

* [Windows Command-Line: Introducing the Windows Pseudo Console (ConPTY) – Windows Command Line Tools For Developers](https://blogs.msdn.microsoft.com/commandline/2018/08/02/windows-command-line-introducing-the-windows-pseudo-console-conpty/)



## リンク

* [Windows Subsystem for Linux + X Windowを1.024倍くらい使いこなすための方法 - Qiita](https://qiita.com/nishemon/items/bb3aca972404f68bfcd6)
* [WSL+Xming環境整備 - 簡潔なQ](https://qnighy.hatenablog.com/entry/2017/10/01/220000)
