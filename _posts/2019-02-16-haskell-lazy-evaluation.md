---
layout: post
title: "Haskell の遅延評価"
description: ""
tags: 
---

もう10年くらい Haskell でプログラムを書いてなくて、すっかり忘れてしまったのだが、
今日は嬉しくなる記事を見つけたのでメモしておく。

著名な Haskeller の山本和彦氏が Haskell の遅延評価に別れを告げている。

* [さようなら遅延評価 - あどけない話](https://kazu-yamamoto.hatenablog.jp/entry/2019/02/15/115630)

昔に読んだ下のメールを思い出す。

* ["interact" behaves oddly if used interactively](https://mail.haskell.org/pipermail/haskell-cafe/2003-October/005190.html)

> Haskell を分岐して遅延評価を捨てた言語を作りたい、
> 博士課程で遅延評価の研究に十分な時間を使い、
> 90%のケースで遅延評価がいい評価戦略でないことを確信した。
> それが必要とされる10%ではプログラムに明示的に宣言したほうがプログラムは明確になるだろう。
> Haskell はいい言語だ、純粋はグッド、型クラスもグッド、
> モナドもグッド、しかし、遅延評価はうんこ。

そのときは某匿名掲示板に[コメントした](http://pc8.5ch.net/test/read.cgi/tech/1149263630/)が同意は得られなかった。
