---
layout: default
title: Blog
---

# Blog Posts

<ul>
  {% assign posts = site.static_files | where: "path", "/blog/" %}
  {% for post in posts %}
    <li>
      <a href="{{ post.path }}">{{ post.name | capitalize }}</a>
    </li>
  {% endfor %}
</ul>
