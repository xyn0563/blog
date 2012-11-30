---
layout: default
title:  welcome! 
---
== 文章列表
{% for post in site.posts %}
* {{ post.date | date_to_string }}<a href="{{ site.baseurl}}{{Bpost.url}}">{{ post.title }}</a>
  {% endfor %}
