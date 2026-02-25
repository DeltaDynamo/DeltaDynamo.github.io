---
layout: page
title: Blog
---

<div class="blog-container">
  {% assign sorted_posts = site.blog | sort: "date" | reverse %}
  {% for post in sorted_posts %}
    <a href="{{ post.url }}" class="blog-card">
      <div class="blog-card-content">
        <div class="blog-date">
          {{ post.date | date: "%d %b %Y" }}
        </div>

        <div class="blog-title">
          {{ post.title }}
        </div>

        {% if post.description %}
          <div class="blog-desc">
            {{ post.description }}
          </div>
        {% endif %}

        {% if post.tags %}
          <div class="blog-tags">
            {% for tag in post.tags %}
              <span class="tag">{{ tag }}</span>
            {% endfor %}
          </div>
        {% endif %}
      </div>
    </a>
  {% endfor %}
</div>