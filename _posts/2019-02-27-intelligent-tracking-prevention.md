---
layout: post
title: "Safari の Intelligent Tracking Prevention 2.1 まとめ"
description: ""
tags: 
---

Safari の Intelligent Tracking Prevention 2.1 を調べたので自分の理解をまとめておく。

## まとめ

* ITP が機械学習を用いてドメイン (eTLD+1) をトラッキング可能ドメインに分類する。
* 判断の重要な要素は３つ、サブリソースをロードするユニークドメイン数、サブフレームをロードするユニークドメイン数、リダイレクトするユニークドメイン数。
* トラッキング可能ドメインに分類されると、third-party context のサブリソースリクエストや iframe 内でのクッキーの送信・設定がブロックされる。
* (トラッキング可能ドメインに分類され、) Safari 通算使用３０日間そのドメインとユーザがインタラクションしなかったら、すべてのデータを消去する。 (cookie, IndexedDB etc)
* トラッキング可能ドメインに分類されても、third-party context での iframe 内で Storage Access API を用いてユーザが許可すれば、その親ページのドメインの third-party context での iframe 内で same-origin クッキーのアクセスが可能になる。
* クライアントサイド・クッキー (document.cookie) の expire 上限は7日。上書きすれば、更新できる。 (ITP によりトラッキング可能ドメインに分類されるかは無関係)

## 参考

* [Intelligent Tracking Prevention 1.0](https://webkit.org/blog/7675/intelligent-tracking-prevention/)
* [Intelligent Tracking Prevention 1.1](https://webkit.org/blog/8142/intelligent-tracking-prevention-1-1/)
* [Intelligent Tracking Prevention 2.0](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/)
* [Intelligent Tracking Prevention 2.1](https://webkit.org/blog/8613/intelligent-tracking-prevention-2-1/)
* [Storage Access API](https://webkit.org/blog/8124/introducing-storage-access-api/)
* John Wilander 氏の tweet
    * https://twitter.com/johnwilander/status/1100386313025445888
    * https://twitter.com/johnwilander/status/1098948746337906689

