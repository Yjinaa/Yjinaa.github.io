---
title: "Study Notes"
layout: category
permalink: /blog/study-notes/
taxonomy: Study Notes
sidebar:
  nav: "blog"
---

{% for post in site.categories.Study Notes %}
  <h2><a href="{{ post.url }}">{{ post.title }}</a></h2>
  <p>{{ post.excerpt }}</p>
{% endfor %}
