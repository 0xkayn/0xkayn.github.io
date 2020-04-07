---
title: home
layout: default
permalink: /home
redirect_from: "/"
---

# Vuggo

## Recent Posts
<ul>
{% for post in site.posts limit: 5 %}
    <li>
        {%- assign date_format = "%Y-%m-%d" -%}
        [ {{ post.date | date: date_format }} ] <a href="{{ post.url | relative_url }}">{{ post.title | escape }}</a>
    </li>
    {%- endfor -%}
</ul>

## Favorite Projects

## Contact
github          Vuggo
twitter         sachalorian
email           vuggoA@gmail.com






