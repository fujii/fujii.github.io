---
layout: post
title: "ビルドスクリプト論"
tags: [cmake]
---

* 書きかけ
* 主に C と C++ のビルドスクリプトを想定
* CMake + Ninja が 2016 年の OSS 界の標準ビルドシステム
* ビルドシステムの選定の参考になるように

## 依存関係

### explicit dependency と implicit dependency

explicit dependency はビルドスクリプトに明示的に書いた dependency。

implicit dependency は gcc -MMD で .d ファイルを生成して Makefile の `-include` で読み込む dependency。
incremental build するのに必要である。

ヘッダが rename されると、ビルドがコケる問題がある。
例えば、以下のような .d ファイルが生成された場合。

~~~make
a.o: a.c a.h
~~~
a.h が b.h に rename されると a.h ファイルを生成する rule がなくて make が失敗する。
この問題は以下のように .d を作りなおしたものを include すれば解決できるように思う。

~~~make
a.o: a.c a.h
a.c:
a.h:
~~~

### link dependency と source dependency

共有ライブラリ libx が liby をリンクする場合、
単純なビルドシステムでは liby のビルド完了後に libx のビルドが行われる。
コンパイルとリンクを分離して考えると、
libx と liby のソースは同時にコンパイル可能である。

### order-only dependency

Make や Ninja には order-only dependency (order-only prerequisites) がある。
これは依存ファイルが更新されてもオブジェクトファイルは更新しない依存ファイルを指定する機能である。

Make のドキュメントでは出力ファイルを置くディレクトリを作成する例が挙げられている。
https://www.gnu.org/software/make/manual/make.html#Prerequisite-Types

共有ライブラリにリンクする場合、共有ライブラリが更新されても基本的にそのアプリケーションをリビルドする必要がない。
なぜならば、共有ライブラリは本質的に API 互換が保たれるし、新しい API が追加された場合もアプリケーションがその新しい API を使用するように変更すればそのアプリケーションはリビルドされるからである。
しかし、API 互換を破壊して API が削除された場合では問題になるが、現実的には無視できる状況である。

## 更新チェック

依存ファイルが更新されたらコマンドを実行し目的ファイルを更新する。
依存ファイルが更新されたと判断する条件は３種類ある。

* mtime の比較し、最も古い目的ファイルよりも依存ファイルが新しかったら (Make)
* 目的ファイルの mtime を記録し、更新されていたら (MSBuild)
* 目的ファイルの内容のハッシュを記録し、ハッシュが変化したら

mtime の比較ではコメントの修正などささいな変更でも、それが依存されるすべてのオブジェクトファイルの更新が必要になる。
ファイルのハッシュを使用することで、例えば .o に変化がなければその先にリンクを行わない。
このようにして時間の掛かる処理を省略できるが、更新チェックにハッシュを計算する処理が重い。

## restat

Ninja には restat 機能がある。
これは更新チェックをコマンド実行後にもう一度行い、更新されていない場合にその後の処理を省略する。

Make は常に restat を行う。
Make は常に restat を行うが、更新チェックは mtime の比較であるため問題がある。
あるコマンドが目的ファイルに変化がない目的ファイル更新しない場合、
次回以降の差分ビルドでもそのコマンドは再度実行されてしまう。


## Autoconf の3つの architecture type

architecture type は cpu-company-system の形式で表される。
例えば x86_64-pc-linux-gnu。
Autoconf 付属の `config.guess` を参照。

* host (クロスコンパイルの対象プラットフォーム)
* build (ビルドを実行するプラットフォーム)
* target (GCC などコンパイラ等において出力となるプラットフォーム)

呼び方はビルドシステムによって異なり (host, build, target) は GYP では (target, host, N/A) と呼ばれる。


## クロスコンパイルの３レベル

* Level 0: システム等に存在するバイナリのコンパイラ・ツールのみを使用する
* Level 1: コンパイラやツールをソースから build platform 用にビルドする
* Level 2: 言語処理系などで一部の機能がその言語で実装されているためビルド中にコンパイルを行う

Level 0 はどんなビルドシステムもサポートしている。

CMake や Automake では Level 1 のクロスコンパイルでは、
まず build platform 用にビルドした後に、host platform 用にビルドする2段階の手順が必要である。


## CMake の特徴

良いところ

* Automake よりも速い
* Ninja generator を使えばビルド時は高速
* 一応、リストがある
* Bourne Shell ほど double quote がいらない (`"${dir}/${name}"` は dir の中に空白があっても quote なしの `${dir}/${name}` でよい)
* 開発が活発

悪いところ

* CMake 時はやや遅い
* 貧弱な言語
* 連想配列がない (変数で代用する `${assoc_${var}}`)
* 関数に返り値がない (変数名を渡してそれに代入する)
* add_custom_command で depfile が非サポート (v3.7 で Ninja generator のみ対応)
* クロスコンパイルのサポートが弱い
* システム変数が大文字
* 柔軟でない (機能が足りなくて困った時は CMake に手を入れるしかない)

* CMakeCache.txt の問題。CMakeLists.txt を変更したが、CMakeCache.txt に古い値がキャッシュされているために発生する問題。CMake 的には CMakeCache.txt はビルドオプションを保存しておいて、 CMakeCache.txt は cmake-gui などで編集するもの。 git pull や svn up で repository の最新を取得した後の差分ビルドの場合の問題。

## ビルドシステムの要件

* ビルドスクリプトは一般的なプログラミング言語と同様に部品(関数やクラスなど)を組み立てて記述できなくてはならない。
* 何度も実行するため速度が重視される
* Unix での標準である GNU Build System のユースケースを満たす (https://www.gnu.org/software/automake/manual/html_node/Autotools-Introduction.html)

なんでもできるビルドシステムがある一方で、
一部のビルドシステムの設計者は意図的にビルドシステムに制限を加える傾向がある。
自由度を高めると好き勝手されて統一感がなくなるためだろう。
こういう思想で設計されたビルドシステムはかゆいところに手が届かないものとなる。

