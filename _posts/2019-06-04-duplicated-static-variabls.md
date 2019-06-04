---
layout: post
title: "static ライブラリ内の static 変数のセグメント重複問題"
description: ""
tags: 
---

大した問題ではないのだが、私の観測範囲でこの問題によく遭遇・目撃する。

ライブラリ A, B, C があり、B と C は A を利用している。
A は static libray で B と C は shared library である。
この場合、B と C の .dll や .so ファイルの中に A が含まれるため、
A の static variable が２重になってしまう。

* [Static variable - Wikipedia](https://en.wikipedia.org/wiki/Static_variable)
* [Data segment - Wikipedia](https://en.wikipedia.org/wiki/Data_segment)
* [.bss - Wikipedia](https://en.wikipedia.org/wiki/.bss)

解決方法は以下のどれか、

* ライブラリ A を shared library にする
* shared library B から A のシンボルも export して、C はそれを利用する
* ライブラリ B と C はライブラリ A を利用していることを隠蔽する
* ライブラリ A の static variable をなくした設計にする

Windows で static な CRT で同じ問題が出る件についての記事。

* [Microsoft Docs: Potential Errors Passing CRT Objects Across DLL Boundaries](https://docs.microsoft.com/en-us/cpp/c-runtime-library/potential-errors-passing-crt-objects-across-dll-boundaries?view=vs-2019)
* [Microsoft Docs: DLL の境界を越えて CRT オブジェクトを渡す場合に発生する可能性のあるエラー](https://docs.microsoft.com/ja-jp/cpp/c-runtime-library/potential-errors-passing-crt-objects-across-dll-boundaries?view=vs-2019)

Hyper Estraier の API は返されたオブジェクトを `free` で返す仕様のため `est_free` の追加依頼がだされている。
この件は私も依頼した記憶があるが見つけられなかった。

* [Hyper Estraier / hyperestraier-users-ja: estcmd gather のエラーいろいろ](https://sourceforge.net/p/hyperestraier/mailman/message/2093416/)
* [Programming Guide of Hyper Estraier Version 1 (Japanese)](https://fallabs.com/hyperestraier/pguide-ja.html#estdoc)

WebKit は WTF と bmalloc が static library で JavaScriptCore やそれ以外が shared library のため問題が発生する。

* [156831 – Heap corruption is detected when destructing JSGlobalObject](https://bugs.webkit.org/show_bug.cgi?id=156831)
* [179914 – Duplicated symbols in libjavascriptcoregtk and libwebkit2gtk can cause crashes in production builds](https://bugs.webkit.org/show_bug.cgi?id=179914)
