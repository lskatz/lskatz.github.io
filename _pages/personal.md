---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: archive
title: Personal blog
permalink: /personal/
#entries_layout: grid
author_profile: true
collection: personal
---

{% assign sorted_posts = site[page.collection] | sort: 'date' | reverse %}
<div class="entries">
  {% for post in sorted_posts %}
    <article>
      <h2><a href="{{ post.url }}">{{ post.title }}</a></h2>
      <p>{{ post.excerpt }}</p>
      <p><small>{{ post.date | date: "%Y-%m-%d" }}</small></p>
    </article>
  {% endfor %}
</div>
