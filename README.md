# 主页

## 查看

[http://warmhug.github.io/](http://warmhug.github.io/)

### tips

- 文件名不要以下划线开始，不然访问不到
- 文件名不要包含单双引号，空格
- 文件内容，不能包含**下划线**

jekyll用了 liquid 作为模板，{{、}}、{%、%}等可能做了liquid的模板语言标记 被解析掉。若要显示这些，用

```text
{% raw %}
...lots of liquid code goes here and it doesn't get interpreted...
{% endraw %}
```

### jekyll 本地服务器调试

```sh
# 安装 https://jekyllrb.com/docs/
jekyll serve     # http://localhost:4000/index.html
jekyll serve --host 0.0.0.0    # http://ip:4000/index.html
```

---

- 使用 https://github.com/allejo/jekyll-toc 或 https://github.com/toshimaru/jekyll-toc

---

皮肤 https://jekyll-themes.com/minima-reboot/

`_includes/head.html` 里的 `{% seo %}` 报错，可加入以下 Google 统计代码

```text
{% if jekyll.environment == 'production' and site.google_analytics %}
  {% include google-analytics.html %}
{% endif %}
```

index.md 里原来的 post 代码

```text
<!-- {% for post in site.posts limit:10 %} -->
```

### 其他

需要 assets 文件夹引用 _sass 里的文件 https://jekyllrb.com/docs/assets/
