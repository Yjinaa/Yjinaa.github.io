---
title: "Event Recaps"
layout: category
permalink: /blog/event-recaps/
taxonomy: Event Recaps
sidebar:
  nav: "blog"
---

{% for post in site.categories.Event Recaps %}
  <h2><a href="{{ post.url }}">{{ post.title }}</a></h2>
  <p>{{ post.excerpt }}</p>
{% endfor %}
