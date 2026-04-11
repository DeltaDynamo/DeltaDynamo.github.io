---
layout: page
title: Blog
---

<div class="search-wrapper">
    <input 
      type="text" 
      id="tag-input" 
      placeholder="Search by tag..."
    >
</div>

<div class="blog-container">
  {% assign sorted_posts = site.blog | sort: "date" | reverse %}
  {% for post in sorted_posts %}
    <a href="{{ post.url }}" class="blog-card" data-tags="{{ post.tags | join: '|' | downcase | strip }}">
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

<script>
document.addEventListener("DOMContentLoaded", function () {

  const input = document.getElementById('tag-input');
  const posts = document.querySelectorAll('.blog-card');

  input.addEventListener('input', () => {
    const value = input.value.toLowerCase().trim();

    posts.forEach(post => {
      const tags = post.getAttribute('data-tags') || "";
      const tagList = tags.split('|');

      if (value === "") {
        post.style.display = "block";
        return;
      }

      const match = tagList.some(tag => tag.includes(value));
      post.style.display = match ? "block" : "none";
    });
  });

  window.filterByTag = function(tag) {
    input.value = tag;
    input.dispatchEvent(new Event('input'));
  };

});
</script>