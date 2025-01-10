---
layout: page
title: Blog
---

<ul>
  {% for post in site.blog %}
    <li>
      <h3>
        <span style="color: grey;">
          {{ post.date | date: "%Y-%m-%d" }}:
        </span>
        <a href="{{ post.url }}">
          {{ post.title }}
        </a>
      </h3>
    </li>
  {% endfor %}
</ul>
