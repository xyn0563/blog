---
layout: default
title:  welcome! 
---
# 文章列表
{% for post in site.posts %}
* <a href="{{ site.baseurl}}{{post.url}}">{{ post.title }}</a> [{{ post.date | date_to_string }}]
{% endfor %}
