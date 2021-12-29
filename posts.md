---
layout: default
title: 随笔
---

<h2>{{ page.title }} ({{ site.posts | size }} 篇)</h2>

<ul class="nav nav-pills mb-4 mt-4" id="postNav">
  <li class="nav-item">
    <a class="nav-link active" href="#">全部</a>
  </li>
  {% for tag in site.tags %}
  <li class="nav-item">
    <a class="nav-link" href='#{{ tag[0] | escape }}'>{{ tag[0] }}</a>
  </li>
  {% endfor %}
</ul>

<ul class="list-unstyled" id="postList"></ul>

{% include api-posts.html %}

