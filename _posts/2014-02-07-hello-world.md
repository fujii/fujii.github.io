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

{% highlight ruby %}
# highlight ruby
def func
    puts "ほげ"
end
{% endhighlight %}

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

* post_url で post へリンク [お好み焼き]({% post_url 2014-06-06-okonomiyaki %})

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

![プログラミング Haskell](http://images-jp.amazon.com/images/P/4274067815.09.MZZZZZZZ.jpg)
リンクなし

[![プログラミング Haskell](http://images-jp.amazon.com/images/P/4274067815.09.MZZZZZZZ.jpg)](http://www.amazon.co.jp/dp/4274067815)
リンクあり

<table style="width:194px;"><tr><td align="center" style="height:194px;background:url(https://www.gstatic.com/pwa/s/v/lighthousefe_20150907.00_p0/transparent_album_background.gif) no-repeat left"><a href="https://picasaweb.google.com/108766616282956994806/2010120402?authuser=0&feat=embedwebsite"><img src="https://lh3.googleusercontent.com/-xtv_NUSgRWc/Uyz7dDG88YE/AAAAAAAAKEw/Naf3tzkYrqU/s160-c-Ic42/2010120402.jpg" width="160" height="160" style="margin:1px 0 0 4px;"></a></td></tr><tr><td style="text-align:center;font-family:arial,sans-serif;font-size:11px"><a href="https://picasaweb.google.com/108766616282956994806/2010120402?authuser=0&feat=embedwebsite" style="color:#4D4D4D;font-weight:bold;text-decoration:none;">2010-12-04 表参道ヒルズ ツリー</a></td></tr></table>
タグ直書き


# Liquid

{% assign fruits = "りんご,みかん,ナシ,Orange" | split: ',' | sort %}

{% for f in fruits %}
* {{ f }}
{% endfor %}

[1]: http://jekyllrb.com/

