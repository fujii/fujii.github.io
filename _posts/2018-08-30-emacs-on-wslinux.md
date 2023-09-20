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

[Ubuntuの高速化～リポジトリ参照先の変更 - Qiita](https://qiita.com/kujiraza/items/c02908e0aed1001b2470)
で紹介されている以下のコマンドがお手軽。

~~~
sudo sed -i.bak -r 's!deb http://archive\S+!deb mirror://mirrors.ubuntu.com/mirrors.txt!' /etc/apt/sources.list
~~~

## start コマンド

Windows の `start` や Cygwin の `cygstart` のようなコマンド。
`wslu` というパッケージに `wslview` というコマンドが追加された。

## Mozc の設定

`sudo apt install emacs-mozc mozc-utils-gui` でインストールする。
どうもこれでは　mozc.el がバイトコンパイルされなていないために Emacs を起動するたびに "Package cl is deprecated" というメッセージが出てしまう。
`sudo apt install emacs-mozc-bin mozc-utils-gui` して mozc.el は M-x list-packages からインストールしたほうが良さそう。

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

Ubuntu 22.04 ではエラーが出るようになった。

> $ /usr/lib/mozc/mozc_tool --mode=config_dialog
> /usr/lib/mozc/mozc_tool: error while loading shared libraries: libQt5Core.so.5: cannot open shared object file: No such file or directory

エラーメッセージで検索すると以下のコマンドで解決することが分かる。

> sudo strip --remove-section=.note.ABI-tag /usr/lib/x86_64-linux-gnu/libQt5Core.so.5

## wsl.conf

Windows の PATH が追加されないように /etc/wsl.conf に設定する。

~~~
[interop]
appendWindowsPath = false
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


## Emacs 29 で main threaed が回り続ける

Emacs 29 から main threaed が回り続ける問題が発生し始めた。
とりあえず環境変数 EMACS_IGNORE_TIMERFD=1 を設定する。

* [#61258 - 29.0.60; pgtk build from git, WSLg, slow, heavy cpu use - GNU bug report logs](https://debbugs.gnu.org/cgi/bugreport.cgi?bug=61258)
* [High CPU usage in WSL with Emacs 29.0.50 : emacs](https://www.reddit.com/r/emacs/comments/urivlg/high_cpu_usage_in_wsl_with_emacs_29050/)
