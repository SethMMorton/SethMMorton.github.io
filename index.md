---
layout: page
title: Welcome!
tagline: Homepage
---
{% include JB/setup %}

## About this blog

Thoughts
    
## Posts

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>
    
## Packages

### Utilities

* [input_reader](https://pypi.python.org/pypi/input_reader) (Python - Text Parsing)
* [natsort](https://pypi.python.org/pypi/natsort) (Python)

### Science

* [NPVol](https://github.com/jensengrouppsu/npvol) (Fortran)
* [RAPID](https://github.com/jensengrouppsu/rapid) (Python - CLI and GUI)
* [NPSpec](https://github.com/SethMMorton/NPSpec) (C++, C, Fortran, Python - Library)

## To-Do

Everything!

