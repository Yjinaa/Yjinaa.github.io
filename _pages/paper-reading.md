---
title: "Paper Reading"
layout: category
permalink: /blog/paper-reading/
taxonomy: Paper Reading
sidebar:
  nav: "blog"
---

{% for post in site.categories.Paper Reading %}
  <h2><a href="{{ post.url }}">{{ post.title }}</a></h2>
  <p>{{ post.excerpt }}</p>
{% endfor %}
