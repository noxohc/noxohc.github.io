---
layout: nox
title: Archives
icon: fas fa-archive
order: 3
---

<div class="nox-archive-list">

  {% assign previous_year = "" %}

  {% for post in site.posts %}
    {% assign current_year = post.date | date: "%Y" %}

    {% if current_year != previous_year %}
      {% unless forloop.first %}
        </div>
      {% endunless %}

      <h2 class="nox-archive-year">
        ✦ {{ current_year }}
      </h2>

      <div class="nox-archive-group">

      {% assign previous_year = current_year %}
    {% endif %}

    <a class="nox-archive-item"
       href="{{ post.url | relative_url }}">

      <span class="nox-archive-date">
        {{ post.date | date: "%m/%d" }}
      </span>

      <span class="nox-archive-title">
        {{ post.title }}
      </span>

      <span class="nox-archive-arrow">→</span>

    </a>

  {% endfor %}

  {% if site.posts.size > 0 %}
    </div>
  {% else %}
    <p class="nox-archive-empty">No posts yet.</p>
  {% endif %}

</div>
