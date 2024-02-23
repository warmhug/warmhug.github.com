---
layout: default
title: 摘记
permalink: /collections
---

<h2>{{ page.title }} ({{ site.collections | size }} 篇)</h2>

<ul class="list-unstyled" id="collectList">
{% assign collections = site.collections | sort: "order" %}
{% for item in collections %}
  <li><a href="{{ item.url }}">{{ item.title }}</a></li>
  <!-- <li>{{ item.content | markdownify }}</li> -->
{% endfor %}
</ul>

<small class="text-secondary">如摘录内容有侵权、联系我邮箱</small>
