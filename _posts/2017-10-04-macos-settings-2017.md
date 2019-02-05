---
layout: post
title: "2017年の macOS 設定"
description: ""
category: 
tags: [macos, emacs]
---

## Karabiner-Elements の設定

Windows, Linux, macOS を使用していると、操作方法の違いはただの苦痛でしかない。
Karabiner-Elements 11 からは旧バージョンの設定が使用できないので新たに設定した。

主に利用するアプリケーションは３つ、仮想ターミナル、Emacs、webブラウザである。
実現したいのは、

* 仮想ターミナルと Emacs では Ctrl が Control, Alt が Option, Windows キーが Command にマップする
* 仮想ターミナルと Emacs 以外では Ctrl が Command , Alt が Option, Windows キーが Control にマップする
* Alt+Tab でウィンドウ切り替え
* ブラウザでは Ctrl+Tab でタブ切り替え

この[設定ファイル](https://gist.github.com/fujii/c77b51d62947bb318389d925dbaf2224)
を `~/.config/karabiner/assets/complex_modifications/fujihiro.json` に置いて、Karabiner-Elements の設定で有効にする。

## 漢字キーで IME のオン/オフ切り替え

* Karabiner-Elements で grave_accent_and_tilde (半角/全角/漢字キー)を F13 キーに変更
* システム環境設定→キーボード→ショートカット→入力ソース→前の入力ソースを選択 を F13 に変更して有効化

詳しい手順はこちら

* http://www.milkmemo.com/entry/windows_kebord_mac
* http://office-m-blog.com/blog/2017/05/06/post-1279/

## Homebrew

好きなのをインストールする。

    brew install aspell binutils coreutils editorconfig findutils zsh
    brew install --with-cocoa emacs

パスを通す。

    PATH=$(brew --prefix coreutils)/libexec/gnubin:$PATH
    PATH=$(brew --prefix findutils)/libexec/gnubin:$PATH


## その他

* ターミナル→環境設定→キーボード→メタキーとして option キーを使用 を有効にする
* 共有フォルダには .DS_Store を生成しない `defaults write com.apple.desktopservices DSDontWriteNetworkStores true`
* ssh サーバを有効にする。 [こちら](https://pc-karuma.net/mac-ssh-login/)を参照。
