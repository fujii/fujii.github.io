---
layout: post
title: "OSS ライセンスの教科書"
description: ""
category: 
tags: 
---
{% include JB/setup %}

近年のソフトウェア業界は OSS なしでは成り立たず、
多くのソフトウェア開発者がなんらかの形で業務で OSS と関わっていると思う。
OSS ライセンスは多くのソフトウェア開発者の必須の知識となっている。

[OSS ライセンスの教科書](http://amzn.asia/d/0hOKpwA)という本を読んだ。
コンセプトはとてもよい。
OSS ライセンスの教科書が確かに必要である。

だったのだが、読んでみると期待していた内容とは違った。
そして、いくつか気になったことがある。

p.125 『静的ライブラリと共有ライブラリ』では
そのタイトル通りの静的ライブラリと共有ライブラリのなんとも当たり前の説明が書かれている。
そんなことは想定読者はすでに知っているのではないだろうか？
知りたいことは、静的ライブラリや共有ライブラリは著作権的に派生著作物となるのかではないだろうか？
この本のスタンスとして込み入った話には深くは踏み込まないという意図があるようなので、
書かれていないのだろうと推測はつくが、
何も書かれていないのでは本当の初学者の読者はそこに複雑な問題が存在することにすら気がつくことができない。

p.124 の LGPL の説明に
「後年、このライセンスはライブラリ以外にも適用可能だという議論を受け、バージョン2.1からは Lesser GNU General Public Licence と改称されました。」
とあるが、これ逆です。

* [あなたの次回のライブラリには劣等GPLを使うべきでない理由 - GNUプロジェクト - フリーソフトウェアファウンデーション](https://www.gnu.org/licenses/why-not-lgpl.ja.html)

に FSF の見解が書かれているとおり、ライブラリにも GPL を適用すべきで、劣等 GPL はライブラリにすら適用しないほうがいいと主張されています。

p.175 の『自社特許の権利行使が制限されるケース』から引用します

        特許について、OSS ではむしろ深刻に考えなくてはならないのが、
        自社の所有する特許権利の権利行使に制限がかかる可能性のほうかもしれません。
        これはすべての OSS に適用されるわけではありません。
        たとえば BSD ライセンスではそのような懸念は杞憂でしょう。

杞憂と書かれていますが、
法律の専門家が書いた
[Open Source Licensing: Software Freedom and Intellectual Property Law](https://www.rosenlaw.com/oslbook.htm)
では "Chapter 5: Academic Licenses" で特許の使用許諾を含む話があります。
杞憂と書いて大丈夫なのでしょうか？


## こんな教科書がほしい

OSSライセンスに潜む複雑な問題をちゃんと取り上げて欲しい。
ライセンス文を読んでも素人にはそこに争点が存在することには気づけないので。

現状では GPL に関しては以下の記事が参考になる。

* [Frequently Asked Questions about the GNU Licenses - GNU Project - Free Software Foundation](https://www.gnu.org/licenses/gpl-faq.en.html)

FSF の GPL の FAQ。これはよくまとまっていて、本当に頻繁にされる質問に対する答えがまとまっている。

* [GNU General Public License - Wikipedia](https://en.wikipedia.org/wiki/GNU_General_Public_License)

FSF の主張だけでは GPL は理解するには不十分で、
動的・静的リンクの解釈の争点は Wikipedia にまとまっている。

* [GNU GPL v3 解説書：IPA 独立行政法人 情報処理推進機構](https://www.ipa.go.jp/osc/license1.html)

これも挙げておく。
