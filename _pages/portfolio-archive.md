---
title: Portfolio
layout: single
permalink: /portfolio/
classes: wide
---

{% assign all_categories = "Games,Bioinformatics Tools,Educational Resources,Recipes" | split: "," %}

{% for category in all_categories %}
<section style="margin-bottom: 2em;">
<h2>{{ category }}</h2>
{% assign items = site.portfolio | where: "portfolio_category", category | sort: "title" %}
<div class="grid__wrapper">
{% for item in items %}
  <div class="grid__item">
    <article class="archive__item">
      <a href="{{ item.link | default: item.url | relative_url }}" {% if item.link %}target="_blank" rel="noopener"{% endif %}>
        {% if item.header.teaser %}
          <div class="archive__item-teaser">
            <img src="{{ item.header.teaser | relative_url }}" alt="{{ item.title }}">
          </div>
        {% endif %}
        <h2 class="archive__item-title no_toc">{{ item.title }}</h2>
        {% if item.excerpt %}
          <p class="archive__item-excerpt">{{ item.excerpt | strip_html | truncate: 160 }}</p>
        {% endif %}
      </a>
    </article>
  </div>
{% endfor %}
</div>
</section>
{% endfor %}
