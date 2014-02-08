---
layout: post
title: "Hello World"
tagline: "これは tagline"
description: ""
category: 
tags: []
---
{% include JB/setup %}

Jekyll はじめました。

# ブロック

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

_強調_
*強調*
__さらに強調__
**さらに強調**

foo_bar_baz

変数 `foo` と関数 `main`
変数`foo`と関数`main`
'hoge
'

