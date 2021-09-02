---
title: mavsh
layout: blogcategory
permalink: /mavsh

---
{% if site.categories["mavsh"].size > 0 %}
<ul>
    {% for post in site.categories["mavsh"] %}
    <li>
        {%- assign date_format = "%Y" -%}
        [{{ post.date | date: date_format }}] <a href="{{ post.url | relative_url }}">{{ post.title | escape }}</a>
    </li>
    {%- endfor -%}
</ul>
{% else %}

<p>No posts in this category</p>

{% endif %}