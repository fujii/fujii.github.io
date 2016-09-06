---
layout: post
title: "ビルドスクリプト論"
description: ""
category: 
tags: []
---
{% include JB/setup %}

* 主に C と C++ のビルドスクリプト
* CMake + Ninja が 2016 年の OSS 界の標準ビルドシステム

## 依存関係

* explicit dependency
* implicit dependency

implicit dependency は gcc -MMD と Makefile の `-include` で読み込む。
ヘッダが rename されると、ビルドがコケる問題がある。

* link dependency
* source dependency

共有ライブラリ libx が liby をリンクする場合、
単純なビルドシステムでは liby のビルド完了後に libx のビルドが行われる。
コンパイルとリンクを分離して考えると、
libx と liby のソースは同時にコンパイル可能である。

* Ninja の order-only dependency

dependency の timestamp が更新されても object は更新しない。

* timestamp と hash

timestamp ではコメントの修正などささいな変更でも、それが依存されるすべての object の recompile が必要になる。
timestamp の代わりに ファイルの hash を使用することで、例えば .o に変化がなければその先にリンクを行わない。


## Autoconf の3つの platform

* --host
* --build
* --target

## クロスコンパイルの３レベル

* Level 0: システム等に存在するバイナリのコンパイラ・ツールのみを使用する
* Level 1: コンパイラやツールをソースから build platform 用にビルドする
* Level 2: 言語処理系などで一部の機能がその言語で実装されているためビルド中にコンパイルを行う

Level 0 はどんなビルドシステムもサポートしている。

CMake や Automake では Level 1 のクロスコンパイルでは、
まず build platform 用にビルドした後に、host platform 用にビルドする2段階の手順が必要である。


## ビルドシステムの要件

* ビルドスクリプトは一般的なプログラミング言語と同様に部品(関数やクラスなど)を組み立てて記述できなくてはならない。
* 何度も実行するため速度が重視される


