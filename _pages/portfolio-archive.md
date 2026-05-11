---
title: Portfolio
layout: single
permalink: /portfolio/
classes: wide
---

<style>
.portfolio-section { margin-bottom: 2.5em; }
.portfolio-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(220px, 1fr));
  gap: 1.25em;
  margin-top: 0.75em;
}
.portfolio-card {
  display: block;
  padding: 1em;
  border: 1px solid #ddd;
  border-radius: 4px;
  text-decoration: none;
  color: inherit;
  transition: box-shadow 0.15s;
}
.portfolio-card:hover { box-shadow: 0 2px 8px rgba(0,0,0,0.12); text-decoration: none; }
.portfolio-card:visited { color: inherit; }
.portfolio-card h3 { margin: 0 0 0.4em; font-size: 1em; color: #52adc8; }
.portfolio-card p  { margin: 0; font-size: 0.85em; color: #494e52; }
</style>

{% assign all_categories = site.data.portfolio_categories %}

{% for category in all_categories %}
{% assign items = site.portfolio | where: "portfolio_category", category | sort: "title" %}
{% if items.size > 0 %}
<div class="portfolio-section">
<h2>{{ category }}</h2>
<div class="portfolio-grid">
{% for item in items %}<a class="portfolio-card" href="{{ item.link | default: item.url | relative_url }}"{% if item.link %} target="_blank" rel="noopener"{% endif %}><h3>{{ item.title }}</h3>{% if item.excerpt %}<p>{{ item.excerpt | strip_html | truncate: 160 }}</p>{% endif %}</a>
{% endfor %}
</div>
</div>
{% endif %}
{% endfor %}
