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


---

# 見出し

## 見出し2
### 見出し3
#### 見出し4
##### 見出し5
###### 見出し6
####### 見出し7


# コードと syntax highlight

4 space

    int main() {
        return 0;
    }

3 backticks

```c
int main() {
    return 0;
}
```

3 tilda

~~~ruby
# comment
def func
    puts "ほげ"
end
~~~

Liquid

{% highlight python %}
def main:
	print "Hello, world"
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

# テーブル

<table>
  <caption>タグ直書きのテーブル</caption>
  <tr>
    <th>りんご
    <th>みかん
    <th>梨
  <tr>
    <td>1
    <td>2
    <td>3
  <tr>
    <td>*強調*
    <td>[Jekyll][1]
    <td>6
</table>


りんご | みかん
------ | -------------
1      | 2
*強調* | [Jekyll][1]

|りんご | みかん        |
|------ | --------------|
|1      | 2             |
|*強調* | [Jekyll][1]   |


[1]: http://jekyllrb.com/

