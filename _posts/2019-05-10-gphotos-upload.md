---
layout: post
title: "Google Photos にアップロード"
description: ""
tags: 
---

ゴールデンウィークなので写真と動画の整理をする。
やりたいことは

* ディレクトリ名をアルバム名として Google Photos にアップロード
* デジカメの画像 JPG と動画 MTS に対応
*「元のサイズ」は容量を消費するので「高品質」でアップロードしたい

「高品質」でのアップロードはどうもAPI が対応していないらしい。
[設定](https://photos.google.com/settings) で [容量の開放] をすると「元のサイズ」から「高品質」に縮小されるので、これでしのぐことにする。

GitHub を探すと様々なプログラムがあるのだが、なかなか要求を満たすものがなかった。

とりあず落ち着いたのは [gphotos-upload](https://github.com/eshmu/gphotos-upload) 。
ディレクトリ名をアルバム名にするシェルスクリプトを組み合わせることにした。

~~~sh
for dir in "$@"; do
    pipenv run python upload.py --auth auth.json --album "$(basename "$dir")" "$dir"/*
done
~~~

## 他の候補

[gphotos-uploader-cli](https://github.com/nmrshll/gphotos-uploader-cli)

* ディレクトリ名をアルバム名にする
* MTS がスキップされる。
* go get でエラーが出ている

~~~
package google.golang.org/api/photoslibrary/v1: cannot find package "google.golang.org/api/photoslibrary/v1" in any of:
        /usr/lib/go-1.10/src/google.golang.org/api/photoslibrary/v1 (from $GOROOT)
        /home/fujii/go/src/google.golang.org/api/photoslibrary/v1 (from $GOPATH)
~~~

どうやら、[２週間前に API が削除されている](https://github.com/googleapis/google-api-go-client/commit/33b7e862cd15f81bfba1c95a4931360da0fece8c)。

[upload-gphotos](https://github.com/3846masa/upload-gphotos)

・公式 API を使っていない
* ディレクトリ名をアルバム名にしない
・動作が怪しい。途中で止まる。

[gpup](https://github.com/int128/gpup)

* これも Go。
* ディレクトリ名をアルバム名にしない
