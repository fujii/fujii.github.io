---
layout: post
title: "ビルドスクリプト論"
tags: [cmake]
---
{% include JB/setup %}

* 主に C と C++ のビルドスクリプト
* CMake + Ninja が 2016 年の OSS 界の標準ビルドシステム

## 依存関係

### explicit dependency と implicit dependency

implicit dependency は gcc -MMD と Makefile の `-include` で読み込む。
ヘッダが rename されると、ビルドがコケる問題がある。

### link dependency と source dependency

共有ライブラリ libx が liby をリンクする場合、
単純なビルドシステムでは liby のビルド完了後に libx のビルドが行われる。
コンパイルとリンクを分離して考えると、
libx と liby のソースは同時にコンパイル可能である。

### order-only dependency

Ninja の order-only dependency がある。
これは依存ファイルが更新されてもオブジェクトファイルは更新しない依存ファイルを指定する機能である。

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


## Autoconf の3つの platform

* --host
* --build
* --target

呼び方はビルドシステムによって異なり (host, build, target) は GYP では (target, host, N/A) と呼ばれる。


## クロスコンパイルの３レベル

* Level 0: システム等に存在するバイナリのコンパイラ・ツールのみを使用する
* Level 1: コンパイラやツールをソースから build platform 用にビルドする
* Level 2: 言語処理系などで一部の機能がその言語で実装されているためビルド中にコンパイルを行う

Level 0 はどんなビルドシステムもサポートしている。

CMake や Automake では Level 1 のクロスコンパイルでは、
まず build platform 用にビルドした後に、host platform 用にビルドする2段階の手順が必要である。


## CMake の印象

良いところ

* Automake よりも速い
* Ninja generator を使えばビルド時は高速
* 一応、リストがある
* Bourne Shell ほど double quote がいらない (`"${dir}/${name}"` は dir の中に空白があっても quote なしの `${dir}/${name}` でよい)
* 開発が活発

悪いところ

* CMake 時はやや遅い ()
* 貧弱な言語
* カスタム depfile が非サポート
* クロスコンパイルのサポートが弱い

## ビルドシステムの要件

* ビルドスクリプトは一般的なプログラミング言語と同様に部品(関数やクラスなど)を組み立てて記述できなくてはならない。
* 何度も実行するため速度が重視される
* Unix での標準である GNU Build System のユースケースを満たす (https://www.gnu.org/software/automake/manual/html_node/Autotools-Introduction.html)
