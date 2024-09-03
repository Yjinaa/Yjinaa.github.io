---
title: "Research Applications"
layout: category
permalink: /blog/research-applications/
taxonomy: Research Applications
sidebar:
  nav: "blog"
---

{% for post in site.categories.Research Applications %}
  <h2><a href="{{ post.url }}">{{ post.title }}</a></h2>
  <p>{{ post.excerpt }}</p>
{% endfor %}
