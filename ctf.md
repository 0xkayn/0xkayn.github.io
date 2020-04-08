---
title: ctf
layout: blogcategory
permalink: /ctf


---

<ul>
    {% for post in site.categories["ctf"] %}
    <li>
        {%- assign date_format = "%Y" -%}
        [{{ post.date | date: date_format }}] <a href="{{ post.url | relative_url }}">{{ post.title | escape }}</a>
    </li>
    {%- endfor -%}
</ul>