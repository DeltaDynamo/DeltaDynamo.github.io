---
layout: default
title: Blog
---

# Blog Posts

<ul>
  {% for post in site.blog %}
    <li>
      <span style="color: grey;">
        {{ post.date | date: "%Y-%m-%d" }}:
      </span>
      <a href="{{ post.url }}" style="font-weight: bold;">
        {{ post.title }}
      </a>
    </li>
  {% endfor %}
</ul>
