---
layout: post
title: "FOSS ライセンス"
tags : []
---
{% include JB/setup %}

FOSS ライセンスについて

## FLOSS か FOSS か OSS なのか

思想の違いとか、配慮とか敬意とかで面倒なので詳しくはこちらを参照。
https://ja.wikipedia.org/wiki/FLOSS

## GPL 離れ

GPLv3 以降は permissive な MIT, BSD ライセンスなどが増加中らしい

* [GPLの採用率、加速度的に減少中](http://opensource.srad.jp/story/11/12/19/0919237/)


## LLVM の特許問題

* [RFC #2: Improving license & patent issues in the LLVM community](http://lists.llvm.org/pipermail/llvm-dev/2016-September/104778.html)

特許問題があって BSD ライセンスから Apache 2 ライセンスへの変更を検討している。
どんな問題かはしらないがこれだろうか？

http://llvm.org/docs/DeveloperPolicy.html#patents

> To the best of our knowledge, LLVM does not infringe on any patents (we have actually removed code from LLVM in the past that was found to infringe). 

過去に侵害を発見したので実際にコードを削除したことがあったそうです。
	
これを思うと、制約の多いライセンスにはそれなりの事情があって、permissive なライセンスではそれが顕在化することもあるということに気を止める必要がある。

もうひとつ興味深いのが以下の文章。

> For example, many prominent open source lawyers consider the GPLv2 and Apache 2 licenses to be compatible already, which means that there is no problem to solve.

多くの卓越したオープンソース弁護士はすでに GPLv2 と Apache 2 license は互換であると考えているそうだ。

これはどういうことだ？
あきらかに Apache 2 license には GPLv2 にはない制約が加わっているのだが。
どのライセンスが GPL とは非互換で混ぜてはいけないという話がよくあるが、
それらの話はすべて素人同士のあてにならない議論だったのか。


## コンパイラの付属しない OS 用の GPL と LGPL のバイナリの配布

この件は FSF に質問をして、ここにそのやり取りを書いた。
http://opensource.stackexchange.com/a/4328/5862
