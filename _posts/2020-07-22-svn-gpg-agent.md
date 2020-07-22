---
layout: post
title: "Subversion で gpg-agent にパスワードを記憶させる"
description: ""
tags: 
---

Subversion 1.12 で plaintext でのパスワードの保存がデフォルトでコンパイル時に無効化された。

* [Apache Subversion 1.12 Release Notes](https://subversion.apache.org/docs/release-notes/1.12.html)

Ubuntu の svn でも無効化されている。
`svn --version` の出力は以下の通り。
~~~
svn, version 1.13.0 (r1867053)
   compiled Mar 24 2020, 12:33:36 on x86_64-pc-linux-gnu

[...]

The following authentication credential caches are available:

* Gnome Keyring
* GPG-Agent
* KWallet (KDE)
~~~

有効化されているときは `* Plaintext cache in /home/<username>/.subversion` が含まれるらしい。

Subversion で gpg-agent を使う際の注意点は
[Apache Subversion 1.8 Release Notes](https://subversion.apache.org/docs/release-notes/1.8.html#gpg-agent)
に書かれている。

* あらかじめ gpg-agent は起動しておく
* cache ID は realm string を MD5 したもの
* gpg-agent の password cache は plaintext よりも安全というわけではない
* 環境変数 GPG_AGENT_INFO と GPG_TTY を指定する (と書かれているが必ずしも必要なさそう)

`svn auth` の出力に `Password cache: gpg-agent` が含まれているときは gpg-agent を利用する。
含まれていないときは利用しないようなので一旦 `svn auth --remove svn.simple` を実行して、空っぽにしておく。

pinentry にはいくつか種類がある。
たとえば GTK 版を利用するときは `sudo apt-get install pinentry-gtk2` でインストールして、
`sudo update-alternatives --config pinentry` で選択する。
pinentry-tty を利用するときは `GPG_TTY` の指定が必要である。

条件はよくわかっていないが、多分ユーザ名が判明していない場合 gpg-agent を経由せずに svn が直接にユーザ名とパスワードを聞いてくるようである。
gpg-agent を経由ではユーザ名は入力しないためだろう。
その場合は、2度目に gpg-agent を経由してパスワードを入力したら gpg-agent にパスワードが記憶される。
そして3度目以降はパスワードなしとなる。

cache ID は realm string を MD5 したものである。
`svn auth` の `Authentication realm: ` 以降の文字列を MD5 にする。
たとえば `echo -n "<https://svn.webkit.org:443> Mac OS Forge" | md5sum` とする。
これは `~/.subversion/auth/svn.simple/8e062ccdd6bc2b444f13bc12dbfd0c61` のようにファイル名としても利用されている。

cache ID を利用して起動している gpg-agent に記憶されているパスワードを取得したり、新たに記憶させたりできる。
~~~
echo GET_PASSPHRASE 8e062ccdd6bc2b444f13bc12dbfd0c61 X prompt description | gpg-connect-agent
~~~

gpg-agent を終了すると記憶したパスワードは忘れてしまう。
plaintext と比較して secure になったわけでもないのに、とても不便になった。
