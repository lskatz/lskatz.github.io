---
layout: page
title: "Lee Katz CV"
permalink: /CV/
---

{% for H in site.data %}
# {{ H.heading }}
  {% for item in site.data.CV[H].items %}
* {{ item.summary }}
  {% endfor %}
{% endfor %}
