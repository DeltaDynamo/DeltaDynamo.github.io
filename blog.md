---
layout: default
title: Blog
---

# Blog Posts

<ul>
  {% for post in site.blog %}
    <li>
      <a href="{{ post.url }}" style="font-weight: bold;">
        {{ post.date | date: "%Y-%m-%d" }}: {{ post.title }}
      </a>
    </li>
  {% endfor %}
</ul>
