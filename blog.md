---
layout: default
title: Blog
---

# Blog Posts

<ul>
  {% for post in site.blog %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>
