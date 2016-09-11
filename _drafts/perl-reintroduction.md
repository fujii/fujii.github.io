---
layout: post
title: "Perl 再入門"
tags: [perl]
---
{% include JB/setup %}

人生において一体何度 Perl に入門したのだろうか。
何度勉強しても次に必要になるときにはすっかり忘れている。
記憶に残らないのは他の言語とは違い独特であることと一貫性がないからだろう。
次に再入門するのがいつになるかはわからないが、いつかその日が来ることだけは間違いないだろう。

## ドキュメント

* [perldoc.perl.org](http://perldoc.perl.org/)
* [perldoc.jp](http://perldoc.jp/)
* [サンプルコードによる「Perl入門」](http://d.hatena.ne.jp/perlcodesample/)

## list と array

似て非なるもの。
意図的に似せているのだろうし、自動的に変換されるので違いに気づきにくい。

array は Perl のデータ構造の一つであり、スカラーのみを保持する。

list は代入や関数呼び出しの実引数など
プログラム上では存在するが Perl のデータ構造は存在しない。
スカラーのみではなく `push(@x, $f);` のように array も要素となる。

scalar context で評価すると list は最後の要素の値となり、 array は要素数となる。

~~~perl
$x = (4, 5); # 5
$x = @{[4, 5]}; # 2
~~~

## Typeglobs

`use strict;` では global symbol の変数は定義できないし、
Perl 5 では typeglob を使うことはほぼないだろう。
global な symbol table の entry が typeglob である。
typeglob は hash で以下のキーを持ち、それらの値としてスカラーやリファレンスを持つ。

* SCALAR (スカラー $)
* ARRAY (配列 @)
* HASH (ハッシュ %)
* CODE (コード &)
* GLOB (型グロブ *)
* IO (入出力)

`@rose` は `@{*rose{ARRAY}}` と同じであり `\@rose` は `*rose{ARRAY}` と同じである。

以下の２つは似ているが注意が必要。

~~~perl
# lily と rose が lily の typeglob が指すようになる
*rose = *lily

# rose の CODE のみ書き換わる
*rose = \&lily;
~~~
