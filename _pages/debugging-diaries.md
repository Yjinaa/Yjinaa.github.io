---
title: "Debugging Diaries"
layout: category
permalink: /blog/debugging_diaries/
taxonomy: Debugging Diaries
sidebar:
  nav: "blog"
---

{% for post in site.categories.Debugging Diaries %}
  <h2><a href="{{ post.url }}">{{ post.title }}</a></h2>
  <p>{{ post.excerpt }}</p>
{% endfor %}
