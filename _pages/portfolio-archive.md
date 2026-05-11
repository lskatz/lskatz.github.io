---
title: Portfolio
layout: single
permalink: /portfolio/
classes: wide
---

{% assign categories = "Games,Bioinformatics Tools,Educational Resources,Recipes" | split: "," %}

{% for category in categories %}
## {{ category }}

{% assign items = site.portfolio | where: "portfolio_category", category | sort: "title" %}
<div class="grid__wrapper">
{% for item in items %}
  {% include archive-single.html type="grid" post=item %}
{% endfor %}
</div>

{% endfor %}
