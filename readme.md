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
