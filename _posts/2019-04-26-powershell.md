---
layout: post
title: "PowerShell 環境"
description: ""
tags: 
---

下の記事を読んで、

* [PowerShell を便利に使うための設定 \| cexenのIT備忘録](https://ite.cexen.info/powershell-%E3%82%92%E4%BE%BF%E5%88%A9%E3%81%AB%E4%BD%BF%E3%81%86%E3%81%9F%E3%82%81%E3%81%AE%E8%A8%AD%E5%AE%9A/)
* [PowerShell基礎文法最速マスター - PowerShell Scripting Weblog](http://winscript.jp/powershell/202)
* [powershell チートシート - Qiita](https://qiita.com/jca02266/items/a0c71ae90d055ab3893c)
* [逆引きUNIXコマンド/Linux,コマンドプロンプト,PowerShellコマンド比較表 - Linuxと過ごす](https://linux.just4fun.biz/?%E9%80%86%E5%BC%95%E3%81%8DUNIX%E3%82%B3%E3%83%9E%E3%83%B3%E3%83%89/Linux%2C%E3%82%B3%E3%83%9E%E3%83%B3%E3%83%89%E3%83%97%E3%83%AD%E3%83%B3%E3%83%97%E3%83%88%2CPowerShell%E3%82%B3%E3%83%9E%E3%83%B3%E3%83%89%E6%AF%94%E8%BC%83%E8%A1%A8)
* [PowerShell Gallery](https://www.powershellgallery.com/)

`notepad $profile` で下の設定をすれば、とりあえず快適。

* `more` だと辛いので、`less` をインストールする
* Emacs key bind に設定
* Shift+Insert でペースト
* diff などのエイリアスを削除

~~~powershell
Set-PSReadlineOption -EditMode Emacs -BellStyle None
Set-PSReadlineKeyHandler -Key Shift+Insert Paste

Remove-Item Alias:diff -Force
Remove-Item Alias:curl -Force
Remove-Item Alias:wget -Force
~~~
