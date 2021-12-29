---
layout: default
title: 摘记
---

<h2>{{ page.title }} ({{ site.collections | size }} 篇)</h2>

<ul class="list-unstyled" id="collectList">
{% for item in site.collections %}
  <li><a href="{{ item.url }}">{{ item.title }}</a></li>
{% endfor %}
</ul>
