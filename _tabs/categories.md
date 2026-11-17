---
layout: nox
title: Categories
icon: fas fa-stream
order: 1
---

<div class="nox-category-list">
  {% for category in site.categories %}
    {% capture category_url %}/categories/{{ category[0] | slugify }}/{% endcapture %}

    <a class="nox-category-item"
       href="{{ category_url | relative_url }}">
      <span class="nox-category-sparkle">✦</span>
      <span class="nox-category-name">{{ category[0] }}</span>
      <span class="nox-category-count">{{ category[1].size }} posts</span>
      <span class="nox-category-arrow">→</span>
    </a>
  {% endfor %}
</div>
