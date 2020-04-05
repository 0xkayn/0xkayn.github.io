---
title: ctf
layout: blogcategory
permalink: /ctf
redirect_from:
    - /ctf/ctf
    - /etc/ctf

---

<ul>
    {% for post in site.categories["ctf"] %}
    <li>
        {%- assign date_format = "%Y-%m-%d" -%}
        [ {{ post.date | date: date_format }} ] <a href="{{ post.url | relative_url }}">{{ post.title | escape }}</a>
    </li>
    {%- endfor -%}
</ul>