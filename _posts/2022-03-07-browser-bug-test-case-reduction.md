---
layout: post
title: "ブラウザのテストケースの最小化"
description: ""
tags: 
---
ブラウザエンジンのバグレポートの Test Case Reduction に役立つかもしれないものを集める。

* [Test Case Reduction \| WebKit](https://webkit.org/test-case-reduction/)
* [How to file a good browser bug](https://web.dev/how-to-file-a-good-bug/#minified-test-case)
* [QA/Minimal Test Cases - MozillaWiki](https://wiki.mozilla.org/QA/Minimal_Test_Cases)

## ページの取得

ブラウザでもページ保存できるし、それで十分なことも多いが、
たまにその方法でローカルに保存したページではお目当てのバグが再現しなくなることがあるので、別の方法も覚えておくと役に立つ。

* [ほんの少しHTMLを変更して検証したい時](https://tech.travelbook.co.jp/posts/mirror-html/)
* [yusukebe/mirror: mirror is command line tool for mirroring a web page.](https://github.com/yusukebe/mirror)
* [gildas-lormeau/SingleFile: Web Extension for Firefox/Chrome/MS Edge and CLI tool to save a faithful copy of an entire web page in a single HTML file](https://github.com/gildas-lormeau/SingleFile)
* [Testcase Reducer – Get this Extension for Firefox](https://addons.mozilla.org/en-US/firefox/addon/testcase-reducer/)

## 整形

{html,css,javascript} {tidy,formatter,beautifier} で検索すればブラウザで利用するものが出てくる。
使用されていない CSS を除去してくれるものもある。
apt-get intall tidy csstidy でインストールできるコマンドのものも覚えておくと役に立つ。

* [HTML Tidy](https://www.html-tidy.org/)
* [CSSTidy](http://csstidy.sourceforge.net/)
