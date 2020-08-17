---
title: home
layout: default
permalink: /

---

# Vuggo
<br>
## Recent Posts
<ul>    
{% for post in site.posts limit: 5 %}
    <li>
        {%- assign date_format = "%Y-%m-%d" -%}
        [ {{ post.date | date: date_format }} ] <a href="{{ post.url | relative_url }}">{{ post.title | escape }}</a>
    </li>
    {%- endfor -%}
</ul>
<br>
## Favorite Projects
<a href="https://github.com/Vuggo/fc">fc</a> - fixed wing flight controller utilizing arduino
<br>
