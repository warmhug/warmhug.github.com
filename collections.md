---
layout: default
title: 摘记
---

<h2>{{ page.title }} ({{ site.collections | size }} 篇)</h2>

<ul class="list-unstyled" id="collectList">
{% assign collections = site.collections | sort: "order" %}
{% for item in collections %}
  <li><a href="{{ item.url }}">{{ item.title }}</a></li>
{% endfor %}
</ul>
