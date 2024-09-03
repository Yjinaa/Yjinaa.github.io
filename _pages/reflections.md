---
title: "Reflections"
layout: category
permalink: /blog/reflections/
taxonomy: reflections
sidebar:
  nav: "blog"
---

{% for post in site.categories.Reflections %}
  <h2><a href="{{ post.url }}">{{ post.title }}</a></h2>
  <p>{{ post.excerpt }}</p>
{% endfor %}
