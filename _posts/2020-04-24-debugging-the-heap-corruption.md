---
layout: post
title: "ヒープ破壊で1週間デバッグした話"
description: ""
tags: 
---

Windows 版 WebKit のヒープ破壊のデバッグに１週間以上費やした。
<https://bugs.webkit.org/show_bug.cgi?id=209847>

仕事で OSS をやっていると、デバッグの比率が高く、しかも知らないコードをデバッグする必要がある。
自身のデバッグ技術と作業効率の向上ために記録を残しておく。
私は Windows 開発に詳しくはないのでもっといい方法があっただろうと思います。

当初は手許の PC 上では再現できず、自動テストサーバー上でのみ再現した。
さいわい msconfig.exe で BOOT option を変更し CPU 数を2にすると、手許の PC でも再現することができた。
さらに、BOOT option を変更しなくても Task Manger で powershell.exe を Set Affinity で CPU ２つに制限することでも、
その PowerShell から自動テストを起動すると再現することができた。

WebKit の自動テストスクリプトはクラッシュを検出すると自動的に ntsd.exe でクラッシュログを採取してくれる。
ExceptionCode は c0000374 (STATUS_HEAP_CORRUPTION) だった。
クラッシュする場所はランダムだが、頻繁にみるのは
CreateProcess の中や Cairo の中や IPC 周辺でのメモリ確保中だった。

release mode の CRT heap でのみ発生し、CRT Debug Heap では再現することはできなかった。
自動テストスクリプトを走らせると再現するが、実行ファイルを debugger で起動して再現する方法は模索しなかったため
Application Verifier を使うことはできなかった。

ヒープ破壊を起こすバグとしてよくあるのは
double free, use-after-free, buffer overrun である。
CPU 数が再現率に影響しているので、 multi threading がらみの race condition や data race が疑われた。

WebKit では多くのオブジェクトが reference counter を用いて生存管理されている。
そして、reference counter の実装には thread safe なものと thread unsafe なものがある。
必ずひとつの thread からしか reference counter の上げ下げを行わないオブジェクトには thread unsafe な reference counter が利用されるが、
万が一、複数の thread から同時にその reference counter を decrement すると、2回 destructor が呼ばれるバグとなる。
以前にそのバグに遭遇したことがあったので、今回もそれを疑った。
(正確には thread unsafe ではなく thread-hostile らしい)

thread safe な reference counter には、破棄された後に reference counter が操作されないかをチェックするデバッグ用コードがあるので、
同様のことを thread unsafe な reference counter に追加した。
しかし、ヒープ破壊は起きるが、そのチェックコードにはヒットしなかった。

つぎに `_heapchk()` を２箇所に入れて挟み込んで発生場所を絞り込む作戦を取った。
頻繁にクラッシュしていた CreateProcess や Cairo は、そもそもそれを呼ぶ前からヒープは壊れていた。
Windows のメッセージループを回していると、壊れることがわかった。
そして、IPC 周りでよく壊れていた。
しかし、UI の thread や IPC 用 thread のメッセージループのメッセージ処理の前と後に `_heapchk()` を入れると、
メッセージ処理前にすでにヒープが壊れていた。
つまり、UI の thread と IPC 用 thread 以外でヒープが壊れていると考えられた。

しかし、他にヒープ破壊しそうな thread に思い当たるものがなかった。
そこで考えたのは CRT Heap が内部で thread を生成し、回収を遅延している可能性だった。
CRT のソースはないので、検証のしようもなかった。

そこで別の allocator として、
Microsoft 製の [mimalloc](https://github.com/microsoft/mimalloc)を試した。
CRT Debug Heap では再現しなかったため心配していたが、幸い mimalloc でも再現した。
しかも、セキュリティ機能をすべて有効 (MI_SECURE=4) にしても再現した。
ドキュメントによるとセキュリティ機能にはガードページや２重開放検知が備わっているそうなので、
これで buffer overrun や double free の可能性は否定された。

次に、CRT Heap と mimalloc を組み合わせて発生場所を絞りこんでいった。
WTF::Vector クラスのみ allocator を mimalloc にしたり、逆に WTF::Vector クラスのみ CRT Heap にしたり。
これで徐々に破壊される箇所が絞りこめた。
結果、IPC で非同期にパイプを ReadFile しているバッファが壊れていた。

IPC の接続を切断するときに ReadFile に渡したバッファは開放するが、
その開始した非同期 ReadFile はキャンセルしていなかった。

IPC 周りでよくクラッシュするのを見ていたが、
ReadFile を非同期に行っていることを知らなかったため、
そこに IO 用に別 thread があることに気づけなかった。
