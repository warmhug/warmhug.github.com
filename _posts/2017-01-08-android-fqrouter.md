---
layout: post
title: "[玩机] Android 作为路由器上网"
tags: [other]
author_name: warmhug
---

> 发在 [https://github.com/fqrouter/fqrouter/issues/329](https://github.com/fqrouter/fqrouter/issues/329)
>
> [http://fqrouter.tumblr.com/android-latest](http://fqrouter.tumblr.com/android-latest)
>
> [http://fqrouter.tumblr.com/post/46855887676/android手机做无线中继路由器](http://fqrouter.tumblr.com/post/46855887676/android手机做无线中继路由器)

已 root 的 Android 手机 fqrouter@2.11.2 shadowsocks 能完美实现灵活的代理穿墙 WiFi 网络。做法如下：

- fqrouter 开启「无线中继」功能，并且关闭 fqrouter 自带的「代理设置」里的所有选项，再**直接退出 fqrouter**。
- 再打开安卓 shadowsocks 客户端（影梭），在 影梭 里设置代理服务器（建议是付费服务器、更稳定点）。
- 在 KingRoot 工具的设置里添加 fqrouter / 影梭 到“内存优化保护名单”里，避免在锁屏时系统杀掉它们的进程。

这样其他设备，只要连上 fqrouter 提供的 WiFi ，就能自动翻墙，完全不用其他任何设置。真的很赞，欢迎大家尝试下！另外 fqrouter 的实现原理和详细解释，[这个文章](http://www.buysellcity.com/chinese/archiver/?tid-138887.html)写得很棒。

这样灵活的地方是：一个 root 的安卓手机充当了网络管理员角色，给其他设备提供翻了墙的 WiFi 网络。
另外为什么使用影梭、而不是 fqrouter 自带的代理功能？因为影梭支持 https 网站，设置也方便，而 fqrouter 自带的代理设置做不到这些。而且如果发现某一个网站打不开、或影梭的某个代理服务器慢了，直接在影梭上切换个代理服务器即可、也很方便。

最后，有一点不明白的是 退出 fqrouter 了，为什么它的“无线中继”功能还可以用？希望高手指教！若有高手能剥离出 fqrouter 的无线中继功能、做成个单独的 app 那就更好了！
