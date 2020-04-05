---
title: etc
layout: blogcategory
permalink: /etc

---

<ul>
    {% for post in site.categories["etc"] %}
    <li>
        {%- assign date_format = "%Y-%m-%d" -%}
        [ {{ post.date | date: date_format }} ] <a href="{{ post.url | relative_url }}">{{ post.title | escape }}</a>
    </li>
    {%- endfor -%}
</ul>