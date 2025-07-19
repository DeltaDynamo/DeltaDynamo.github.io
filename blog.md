---
layout: page
title: Software Engineering Wisdom
---

<ul>
  {% assign sorted_posts = site.blog | sort: "date" | reverse %}
    {% for post in sorted_posts %}
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
