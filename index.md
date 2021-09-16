---
title: home
layout: default
permalink: /

---
 
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
<a href="https://github.com/0xkayn/fc">fc</a> - fixed wing flight controller utilizing arduino
<a href="https://github.com/0xkayn/Valkyrie">Project Valkyrie</a> - MAVLink protocol mod to enable remote command execution on a RPI0w connected to an arducopter over a TBS Crossfire device

<br>
