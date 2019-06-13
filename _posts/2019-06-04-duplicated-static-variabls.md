---
layout: post
title: "static ライブラリ内の static 変数のセグメント重複問題"
description: ""
tags: 
---

大した問題ではないのだが、私の観測範囲でこの問題によく遭遇・目撃する。

ライブラリ A, B, C があり、B と C は A を利用している。
A は static ライブラリで B と C は共有ライブラリである。
この場合、B と C の .dll や .so ファイルの中に A が含まれるため、
A の static variable が２重になってしまう。

* [Static variable - Wikipedia](https://en.wikipedia.org/wiki/Static_variable)
* [Data segment - Wikipedia](https://en.wikipedia.org/wiki/Data_segment)
* [.bss - Wikipedia](https://en.wikipedia.org/wiki/.bss)

解決方法は以下のどれか、

* ライブラリ A を共有ライブラリにする
* 共有ライブラリ B から A のシンボルも export して、C はそれを利用する
* ライブラリ B と C はライブラリ A を利用していることを隠蔽する
* ライブラリ A の static variable をなくした設計にする

Windows で static な CRT で同じ問題が出る件についての記事。

* [Microsoft Docs: Potential Errors Passing CRT Objects Across DLL Boundaries](https://docs.microsoft.com/en-us/cpp/c-runtime-library/potential-errors-passing-crt-objects-across-dll-boundaries?view=vs-2019)
* [Microsoft Docs: DLL の境界を越えて CRT オブジェクトを渡す場合に発生する可能性のあるエラー](https://docs.microsoft.com/ja-jp/cpp/c-runtime-library/potential-errors-passing-crt-objects-across-dll-boundaries?view=vs-2019)

Hyper Estraier の API は返されたオブジェクトを `free` で開放する仕様のため `est_free` の追加の要望があった。
この件は私も依頼した記憶があるが見つけられなかった。
`cbfree` というのがあるようだ。

* [Hyper Estraier / hyperestraier-users-ja: estcmd gather のエラーいろいろ](https://sourceforge.net/p/hyperestraier/mailman/message/2093416/)
* [Programming Guide of Hyper Estraier Version 1 (Japanese)](https://fallabs.com/hyperestraier/pguide-ja.html#estdoc)

WebKit は WTF と bmalloc が static library で JavaScriptCore やそれ以外が共有ライブラリのため問題が発生する。

* [156831 – Heap corruption is detected when destructing JSGlobalObject](https://bugs.webkit.org/show_bug.cgi?id=156831)
* [179914 – Duplicated symbols in libjavascriptcoregtk and libwebkit2gtk can cause crashes in production builds](https://bugs.webkit.org/show_bug.cgi?id=179914)

cairo の API はコンテキストを引数に取るので一見すると問題なさそうに思ってしまうが、
[static 変数を持っていて](https://github.com/freedesktop/cairo/blob/bab53d91a8543e2ddb15f9dce98ebb3f9bcd5d22/src/cairo-path.c#L54)、
[そのアドレスの比較をしている](https://github.com/freedesktop/cairo/blob/bab53d91a8543e2ddb15f9dce98ebb3f9bcd5d22/src/cairo-path.c#L368)
ためこの問題に遭遇したことがある。

隠蔽する解決方法では反対の問題が起こりうる。
共有ライブラリ B と C の中に A を隠蔽して A のオブジェクトを外に出さないようにする。
だれかがそのライブラリをすべて static ライブラリか、すべて共有ライブラリとしてビルドしてしまうと、
２つあるべき A がひとつになってしまう。
そのため、A のライブラリの初期化・終了処理が２度呼ばれたり、マルチスレッドだとデータレースが発生する問題が考えられる。

この場合の解決方法としては、API はコンテキストを引数に取るようにして static 変数を利用しないのが望ましい。
static 変数をさけられない場合はライブラリの初期化・終了処理が複数回呼ばれたり、マルチスレッドから呼ばれても大丈夫なようにする。
もしくは、ライブラリ A を内包しない。ライブラリ A の初期化・終了処理は B や C のアプリ側が呼ぶことになる。
もしくは、ライブラリ A を２つにコピーしてリネームしてから B と C に隠蔽する。
