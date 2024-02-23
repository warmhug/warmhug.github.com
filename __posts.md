---
layout: default
title: 随笔
permalink: /posts
---

<h2>{{ page.title }} ({{ site.posts | size }} 篇)</h2>

<div class="mb-4 mt-4" id="postNav">
  <a class="btn btn-sm btn-primary" href="#" role="button">全部</a>
  {% for tag in site.tags %}
  <a class="btn btn-sm btn-light" href="#" role="button" href='#{{ tag[0] | escape }}'>{{ tag[0] }}</a>
  {% endfor %}
</div>

<ul class="list-unstyled" id="postList"></ul>

{% include api-posts.html %}
