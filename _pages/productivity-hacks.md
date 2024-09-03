---
title: "Productivity Hacks"
layout: category
permalink: /blog/productivity-hacks/
taxonomy: Productivity Hacks
sidebar:
  nav: "blog"
---

{% for post in site.categories.Productivity Hacks %}
  <h2><a href="{{ post.url }}">{{ post.title }}</a></h2>
  <p>{{ post.excerpt }}</p>
{% endfor %}
