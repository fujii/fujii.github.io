---
layout: page
title: Fujii Hironori
tagline: 
---
{% include JB/setup %}

GitHub Pages はじめました。

* {{ site.author.email }}
* [Facebook](https://www.facebook.com/fujii.hironori)
* Twitter: [@{{ site.author.twitter }}](https://twitter.com/{{ site.author.twitter }})

## Blog

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

