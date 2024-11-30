# [http://warmhug.github.io/](http://warmhug.github.io/)

## tips

- 文件名 不能`以下划线开头 包含单双引号 空格`.
- 文件内容 不能包含`下划线`.
- jekyll 用 liquid 作为模板，`{{、}}` `{%、%}` 等会被解析掉。若要显示这些，用 `{% raw %} ...liquid code goes here {% endraw %}`

- 皮肤 https://jekyll-themes.com/minima-reboot/
- 需要 assets 文件夹引用 _sass 里的文件 https://jekyllrb.com/docs/assets/
- 使用 https://github.com/allejo/jekyll-toc 或 https://github.com/toshimaru/jekyll-toc

## serve

```sh
jekyll serve     # http://localhost:4000/index.html
jekyll serve --host 0.0.0.0

# 安装 https://jekyllrb.com/docs/
ruby -v && gem -v && gcc -v && g++ -v && make -v

# https://jekyllrb.com/docs/troubleshooting/
# mac 自带的 ruby 在运行 jekyll 时有问题
gem env  # 查看
```
