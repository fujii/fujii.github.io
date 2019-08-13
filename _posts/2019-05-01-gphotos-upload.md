---
layout: post
title: "Google Photos にアップロード"
description: ""
tags: 
---

溜まった写真と動画の整理をする。
やりたいことは

* ディレクトリ名をアルバム名として Google Photos にアップロードしたい
* デジカメの画像 (.JPG) と動画 (.MTS, .MP4) に対応
* 「元のサイズ」は容量を消費するので「高品質」でアップロードしたい

[Google Photos APIs](https://developers.google.com/photos/) は「高品質」でのアップロードはできない。
あらかじめ縮小する必要がありそう。
[設定](https://photos.google.com/settings) で [容量の開放] をすると「元のサイズ」から「高品質」に縮小されるので、これでしのぐことにする。

GitHub を探すと様々なプログラムがあるのだが、なかなか要求を満たすものがなかった。

[gphotos-upload](https://github.com/eshmu/gphotos-upload)

* Python
* ディレクトリ名をアルバム名にしない
* ライセンスが不明

とりあえずはこれに落ち着いた。
ディレクトリ名をアルバム名にするシェルスクリプトを組み合わせることにした。

~~~sh
for dir in "$@"; do
    pipenv run python upload.py --auth auth.json --album "$(basename "$dir")" "$dir"/*
done
~~~

[gphotos-uploader-cli](https://github.com/nmrshll/gphotos-uploader-cli)

* Go
* ディレクトリ名をアルバム名にする
* 古い写真をアップロードすると1写真ごとに1アルバムになる [issue #19](https://github.com/gphotosuploader/gphotos-uploader-cli/issues/19)

[upload-gphotos](https://github.com/3846masa/upload-gphotos)

* ディレクトリ名をアルバム名にしない
* 公式 API を使っていないためか、動作が怪しい気がする。途中で止まる。

[gpup](https://github.com/int128/gpup)

* Go
* ディレクトリ名をアルバム名にしない
