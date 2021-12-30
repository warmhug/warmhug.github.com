---
layout: default
title: Warmhug's blog
---

{% if site.paginate %}
  {% assign posts = paginator.posts %}
{% else %}
  {% assign posts = site.posts %}
{% endif %}

<ul class="list-unstyled">
  {% for post in posts %}
  <li class="py-2">
    <h1 class="mt-1 mb-3 h3">
      <a href="{{ post.url | relative_url }}">{{ post.title | escape }}</a>
    </h1>
    <div class="text-justify">
      {{ post.excerpt }}
      ...
    </div>
  </li>
  {% endfor %}
  <li><a href="posts.html">...</a></li>
</ul>

{% if site.paginate %}
<nav class="py-1" aria-label="Blog page navigation">
  {% if paginator.previous_page %}
    <a href="{{ paginator.previous_page_path | relative_url }}">&laquo; Previous</a>
  {% endif %}

  Page: {{ paginator.page }} of {{ paginator.total_pages }}

  {% if paginator.next_page %}
    <a href="{{ paginator.next_page_path | relative_url }}">Next &raquo;</a>
  {% endif %}
</nav>
{% endif %}

```
  I hear and I forget.  I see and I remember.  I do and I understand.

  Love ends when you stop caring.  Friendship ends when you stop sharing.
  Life stops when you stop dreaming.  Hope ends when you stop believing.
```
