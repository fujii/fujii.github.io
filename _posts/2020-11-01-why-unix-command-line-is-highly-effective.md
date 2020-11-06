---
layout: post
title: "なぜ Unix コマンドラインが高効率なのか"
description: ""
tags: 
---

こんなことはプログラマにとって常識であり、すでに語り尽くされているし、『UNIXという考え方』という書籍すら存在している。
しかしながら、先日に[ある問題](https://webkit.org/b/218375)を調査しバグレポートをしていて、改めてその便利さをしみじみと感じたので書きたくなった。
Unix コマンドラインが高効率である理由を考えると以下の要因が挙げられる。

1. 豊富なコマンド郡により多くの作業が one-liner で解決する
2. programmable
3. cross-platform
4. プログラマ同士の共通言語として使える

前述のある問題とはあるプログラムでローカルの web server に幾度もアクセスしていると、低頻度で失敗するものだった。
そのときに調査に使った one-liner は以下のようなものだった。

~~~
seq 1000 | xargs -n 1 -P 30 curl --no-progress-meter http://localhost/ -o
yes http://localhost/ | head -1000 | ./my-programm > ouput.txt
grep XYZ ouput.txt | cat -n | grep ABC
~~~

まず curl コマンドを並列に30個起動してローカルの web server に 1000 回アクセスしその結果をファイルに保存した。
そのファイルを md5sum でチェックしてその web server は負荷をかけても問題が発生しないことを確認した。
xargs に便利な -P オプションがあるお陰で one-liner で済んだ。

次にその URL を1000行繰り返したテキストをプログラムに入力している。
わざわざテキストエディタで1000行のファイルを作成する必要はない。
プログラマ同士の共通言語として使えるため利用したコマンドラインをそのままバグレポートに記載している。
そうすることでわざわざ「テキストエディタで1000行のファイルを作成しました」などと説明する必要がない。

出力結果から XYZ を含む行を抜き出し、行番号をつけて、ABC を含む行を抜き出した。
これでどれくらいの頻度で失敗したかを確認することができた。

今調べてみると curl にはそもそも --parallel と --parallel-max というオプションがあることに気がついた。
しかしながら重要なことは xargs ならどんなコマンドとも組み合わせることができる (programmable である) ということである。

Windows でも WSL や Cygwin などを用いて豊富な Unix コマンド郡が利用できる。
一度覚えた知識を Mac, Linux, Windows で利用できる。
 
ただし問題もあって、コマンドやコマンドオプションに方言があるため気をつける必要がある。
上記のコマンドラインでも xargs -P や cat -n などは GNU 拡張である。
特に Mac でこの問題に頻繁に遭遇し、 cross-platform というのはやや誇張である。
また、find や xargs でコマンドを組み合わせるときに独特の表記 (find の `{}` や xargs の `-I replace-str`) が必要になる点は美しくはない。
その点において ScriptBlock を引数に取る PowerShell の ForEach-Object などは現代的である。

PowerShell は基本 Windows のみなので③と④を満たさない。
しかしながら、Windows 限定の作業には同様の理由で便利である。
