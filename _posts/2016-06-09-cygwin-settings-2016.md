---
layout: post
title: "2016 年の Cygwin 設定"
description: ""
category: 
tags: [windows]
---
{% include JB/setup %}

Cygwin を使うとその遅さや fork の不安定さ、Posix の不完全さによりずっと不満な状態が続く。
数年ごとに脱 Cygwin をするものの結局 Cygwin なしの Windows 生活も不便だったことを思い出す。
これを繰り返している。
最近は emacs-w32 の登場で何度目かの Cygwin との接近期間である。

## ドキュメントを読む

[Cygwin User's Guide](https://cygwin.com/cygwin-ug-net.html)
はよく書けているので必ず読む。
昔とは違うこともある。既に mkpasswd は非推奨である。
cygstart, cygcheck, cygpath などは便利なコマンドである。

## setup-x86_64.exe を取得

setup-x86_64.exe は頻繁に更新されるのでバッチファイルを作成しておく。

update-cygwin-setup.bat

~~~
powershell -Command "(New-Object System.Net.WebClient).DownloadFile('https://www.cygwin.com/setup-x86_64.exe', 'setup-x86_64.exe')"
~~~

## mount table

home directory が C:\cygwin64\home 以下では不便なので、
C:\home に変更する。

/etc/fstab に以下を追加します。

    c:/home /home ntfs binary 0 0

noacl をつけるなら

    c:/home /home ntfs binary,noacl 0 0

詳しくは https://cygwin.com/cygwin-ug-net/mount.html を参照。

ちなみに、こうすると Cygwin 形式の /home 以下の path を Windows アプリに渡しても開ける。

    notepad ~/.zshrc

## symbolic link

よく使う directory へは symbolic link を作成する。

    ln -s $(cygpath --desktop) ~/Desktop
    ln -s $(cygpath --mydocs) ~/Documents

`cygstart` はよく使うけど長いので `x` にする。

    ln -s $(which cygstart) ~/bin/x

こうすると `x .` で current directory をエクスプローラーで開ける。

## ipconfig の文字化け

ipconfig が Shift_JIS (CP932) で出力するために文字化けする。
chcp で UTF-8 にコンソールを切り替えると、ipconfig の出力は英語に切り替わる。

    cmd /C "chcp 65001"

なぜか zsh は chcp が直接使えるので `.zprofile` に以下を設定する。

    chcp 65001

言語設定を英語にしてもよい。

    コントロールパネル > 地域と言語 > Unicode 対応ではないプログラムの言語

## login shell と ssh-agent

mintty のショートカットに指定する。

    C:\cygwin64\bin\mintty.exe -t Terminal -i /Cygwin-Terminal.ico -- /usr/bin/ssh-agent /usr/bin/zsh --login

あとは .zprofile で ssh-add で鍵を追加する。

## emacs-w32

IME patch は当たっていないので、IME での入力は困難な状態です。
日本語入力は Google 日本語入力 + emacs-mozc を利用している。

* MELPA の設定をし、[mozc](https://melpa.org/#/mozc) をインストール。
* Google 日本語入力をインストール。
* [NTEmacs @ ウィキ - emacs-mozc を動かすための設定（mozc_emacs_helper コンパイル編）](http://www49.atwiki.jp/ntemacs/pages/50.html) から mozc_emacs_helper.exe を取得
* [NTEmacs @ ウィキ - emacs-mozc を動かすための設定（emacs 設定編）](http://www49.atwiki.jp/ntemacs/pages/48.html) から以下の設定を行う

~~~
;; Windows の mozc では、セッション接続直後 directモード になるので hiraganaモード にする
(advice-add 'mozc-session-execute-command
            :after (lambda (&rest args)
                     (when (eq (nth 0 args) 'CreateSession)
                       (mozc-session-sendkey '(hiragana)))))
~~~


## ACL

ACL でおかしなことになったら setfacl --remove-all を思い出す。
<https://cygwin.com/cygwin-ug-net/setfacl.html>

あらかじめ mount option で noacl を設定してもよいが、
一長一短である。

