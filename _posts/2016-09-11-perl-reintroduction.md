---
layout: post
title: "Perl 5 再入門"
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

list と array は似て非なるもの。
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
typeglob は連想配列で以下のキーを持ち、それらの値としてスカラーやリファレンスを持つ。

* SCALAR (スカラー $)
* ARRAY (配列 @)
* HASH (ハッシュ %)
* CODE (コード &)
* GLOB (型グロブ *)
* IO (ファイルハンドル)

typeglob のなかに GLOB があるのは混乱を招くが
Advanced Perl Programming の[Figure 20.6: Glob value structure](http://docstore.mik.ua/orelly/perl/advprog/ch20_03.htm#ch20-38386)には GLOB は存在していないので、
内部的には存在しないのだろう。

global symbol において
`@rose` は `@{*rose{ARRAY}}` と同じであり、
`\@rose` は `*rose{ARRAY}` と同じである。

以下の２つは似ているがやっていることは違うので注意が必要。

~~~perl
# lily と rose が lily の typeglob が指すようになる
*rose = *lily

# rose の CODE のみ書き換わる
*rose = \&lily;
~~~
