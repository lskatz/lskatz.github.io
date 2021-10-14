---
layout: page
title: "Lee Katz, Ph.D. CV"
permalink: /CV/
---

{% for H in site.data }
# {{ H }}
  {% for item in site.data.CV[H].items %}
* {{ item.summary }}
  {% endfor %}
{% endfor %}
