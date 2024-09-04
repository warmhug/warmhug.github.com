# [http://warmhug.github.io/](http://warmhug.github.io/)

## tips

- 文件名不要以下划线开始，不然访问不到
- 文件名不要包含单双引号，空格
- 文件内容，不能包含**下划线**

jekyll用了 liquid 作为模板，`{{、}}` `{%、%}` 等会被解析掉。若要显示这些，用

```js
{% raw %}
...lots of liquid code goes here and it doesn't get interpreted...
{% endraw %}
```

- 皮肤 https://jekyll-themes.com/minima-reboot/
- 需要 assets 文件夹引用 _sass 里的文件 https://jekyllrb.com/docs/assets/
- 使用 https://github.com/allejo/jekyll-toc 或 https://github.com/toshimaru/jekyll-toc


## serve

```sh
# 安装 https://jekyllrb.com/docs/
jekyll serve     # http://localhost:4000/index.html
jekyll serve --host 0.0.0.0    # http://ip:4000/index.html
```

## troubleshooting

https://jekyllrb.com/docs/troubleshooting/

mac 自带的 ruby 在运行 jekyll 时有问题，brew 安装 ruby@3
在 .zshrc 里加入 ruby@3 的 bin 路径。
过程中有些依赖出错、就相应单独安装，比如
`brew install libyaml / readline / ...`

```sh
# 先关闭sip (System Integrity Protection in macOS v10.11)
gem install -n /usr/local/bin jekyll
# 安装 jekyll plugins
gem install jekyll-feed jekyll-paginate jekyll-redirect-from jekyll-seo-tag
```
