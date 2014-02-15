---
layout: post
title: "Hello World"
tagline: "これは tagline"
description: ""
category: 
tags: []
---
{% include JB/setup %}

[Jekyll][1] はじめました。

# リスト

* 赤
* 黄
* 青

1. red
2. yellow
3. blue


# 見出し

## 見出し2

### 見出し3

#### 見出し4

##### 見出し5

###### 見出し6


# コードと syntax highlight

    /* スペース4つでインデント */
    int main() {
        return 0;
    }


~~~ruby
# チルダ3つ
def func
    puts "ほげ"
end
~~~

# inline

* _強調_
* *強調*
* __さらに強調__
* **さらに強調**

* foo_bar_baz

* 変数 `foo` と関数 `main`
* 変数`foo`と関数`main`


* [Google](http://google.com)
* <http://google.com>
* http://google.com

# テーブル

## テーブル1

りんご | みかん
------ | -------------
1      | 2
*強調* | [Jekyll][1]

## テーブル2

|りんご | みかん        |
|------ | --------------|
|1      | 2             |
|*強調* | [Jekyll][1]   |

## タグ直書きのテーブル

<table>
  <tr>
    <th>りんご</th>
    <th>みかん</th>
    <th>梨</th>
  </tr>
  <tr>
    <td>1</td>
    <td>2</td>
    <td>3</td>
  </tr>
  <tr>
    <td>*強調*</td>
    <td>[Jekyll][1]</td>
    <td>6</td>
  </tr>
</table>

# 画像

表参道ヒルズのツリーだよ。
![表参道ヒルズのツリー](https://lh3.googleusercontent.com/-hqCz6XkYPKM/TPpQKEvoJEI/AAAAAAAAAEY/n0VJcyZWtDc/s288/101204_203234.jpg)
リンクなし

表参道ヒルズのツリーだよ。
[![表参道ヒルズのツリー](https://lh3.googleusercontent.com/-hqCz6XkYPKM/TPpQKEvoJEI/AAAAAAAAAEY/n0VJcyZWtDc/s288/101204_203234.jpg)](http://www.omotesandohills.com/)
リンクあり

<table style="width:auto;"><tr><td><a href="https://picasaweb.google.com/lh/photo/vubXbaSTMKeUhjYRqLfWXNMTjNZETYmyPJy0liipFm0?feat=embedwebsite"><img src="https://lh3.googleusercontent.com/-hqCz6XkYPKM/TPpQKEvoJEI/AAAAAAAAAEY/n0VJcyZWtDc/s144/101204_203234.jpg" height="144" width="108" /></a></td></tr><tr><td style="font-family:arial,sans-serif; font-size:11px; text-align:right">送信者 <a href="https://picasaweb.google.com/fujii.hironori/TXNihG?authuser=0&feat=embedwebsite">表参道ヒルズ ツリー</a></td></tr></table>
タグ直書き

[1]: http://jekyllrb.com/

