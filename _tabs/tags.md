---
layout: nox
title: Tags
icon: fas fa-tags
order: 2
---

<div class="nox-category-list">
  {% assign sorted_tags = site.tags | sort %}

  {% for tag in sorted_tags %}
    {% capture tag_url %}/tags/{{ tag[0] | slugify }}/{% endcapture %}

    <a class="nox-category-item"
       href="{{ tag_url | relative_url }}">
      <span class="nox-category-sparkle">✦</span>
      <span class="nox-category-name">{{ tag[0] }}</span>
      <span class="nox-category-count">{{ tag[1].size }} posts</span>
      <span class="nox-category-arrow">→</span>
    </a>
  {% endfor %}
</div>
