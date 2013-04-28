---
layout: page
title: Welcome!
tagline: Homepage
---
{% include JB/setup %}

## About me

I am self-taught programmer with a computational/theoretical chemistry background.
My language of choice is Python, but I did most of my graduate work in Fortran.
I also use C++, Perl, Tcl, Bash & Csh.  I enjoy scripting, particularly text parsing
and automation.  I also enjoy writing numerical and scientific code.

## Packages

### Utilities

* [input_reader](https://pypi.python.org/pypi/input_reader) (Python - Text Parsing)
* [natsort](https://pypi.python.org/pypi/natsort) (Python)

### Science

* [NPVol](https://github.com/jensengrouppsu/npvol) (Fortran)
* [RAPID](https://github.com/jensengrouppsu/rapid) (Python - CLI and GUI)
* [NPSpec](https://github.com/SethMMorton/NPSpec) (C++, C, Fortran, Python - Library)

## Posts

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>
