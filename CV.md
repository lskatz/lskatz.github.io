---
layout: page
title: "Lee Katz CV"
permalink: /CV/
---

# Awards

{% for item in site.data.CV.awards.items %}
* {{ item.summary | replace: "\*", "" }}
{% endfor %}

