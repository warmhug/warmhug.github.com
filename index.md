---
layout: default
title: Warmhug's blog
---

<ul class="list-unstyled">
  {% for post in site.posts limit:10 %}
  <li><a href="{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
  <li><a href="posts.html">...</a></li>
</ul>

```
  I hear and I forget.  I see and I remember.  I do and I understand.

  Love ends when you stop caring.  Friendship ends when you stop sharing.
  Life stops when you stop dreaming.  Hope ends when you stop believing.
```
