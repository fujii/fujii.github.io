---
layout: page
title: Fujii Hironori
tagline: 
---
{% include JB/setup %}

* [{{ site.author.email }}](email:{{ site.author.email }})
* [Twitter](https://twitter.com/{{ site.author.twitter }})
* [Facebook](https://www.facebook.com/fujii.hironori)
* [GitHub](https://github.com/fujii)
* [Google+](https://plus.google.com/108766616282956994806)

## Random Notes

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title | xml_escape }}</a></li>
  {% endfor %}
</ul>

