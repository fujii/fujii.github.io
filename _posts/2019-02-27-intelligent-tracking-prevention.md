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


## Google Analytics のユーザ識別

* [Google Analytics Cookie Usage on Websites](https://developers.google.com/analytics/devguides/collection/analyticsjs/cookie-usage)
* [Cookies and User Identification](https://developers.google.com/analytics/devguides/collection/analyticsjs/cookies-user-id)

> Note: unlike cookies, localStorage is bound by the same-origin policy. If parts of your site are on different subdomains,
> or if some pages use http and others pages use https, you cannot use localStorage to track users between those pages.
> For this reason, cookies continues to be the officially recommended way to store the Client ID.

* first-party cookie を利用するのは cross-site tracking をしないためのようである
* localStorage ではなく cookie を利用するのは、same-origin policy のため
* cookie の使うため毎度 HTTP request についてしまうが、これは無意味なのだろう


## 感想

前々から広告業界は Cookie Matching や Cookie Sync と呼ばれるユーザID の名寄せをやっている。
cookie を使わなくてもユーザを識別する方法は他にもある。
third-party cookie をブロックしたら first-party cookie を使われだしたので、first-party cookie の有効期限上限を7日間になったり、
first-party cookie を設定するために無駄に redirect されるようになったり、ユーザに不利益になってない？
インターネットユーザであればだれでも月に一度は何かしらのインタラクトするであろう Google, Facebook, YouTube のような有名サービスを有する業者が有利になって、弱小広告業者が不利になるのではないだろうか？
そんななんてことを考えていたら、すでに mala 氏が下の記事で書いていた。

* [Firefox 22のCookieに関するポリシー変更(予定)についての意見書](https://gist.github.com/mala/5028502)

いくつかリンクを貼っておく。

* [サードパーティCookieの歴史と現状 Part1 前提知識の共有](http://d.hatena.ne.jp/mala/20111125/1322210819)
* [Google、SafariのサードパーティCookie問題で2250万ドルの制裁金を支払う方向へ。 | スラド IT](https://it.srad.jp/story/12/07/12/0253208/)
* [Cookieを使わずにユーザーを追跡する仕組みが普及しつつある | スラド IT](https://it.srad.jp/story/14/07/22/0613210/)
