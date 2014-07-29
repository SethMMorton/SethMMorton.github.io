---
layout: page
title: Seth M. Morton
tagline: Homepage
---
{% include JB/setup %}

[Projects](/projects/) | [About Me](/about/)

## Posts

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>
