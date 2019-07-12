---
layout: post
title: "mock工具和RESTful设计细节探讨"
tags: [js]
author_name: warmhug
---

## mock工具

我们现在的mock平台或项目里的数据mock工具，大多都有这样的基本特点：

- 利用类似[mockjs](http://mockjs.com/)这样的模拟数据生成器来生成mock数据，有些能自动生成API文档
- 一般只用在项目开始，前后端约定好了数据接口名，但后台数据还没能提供出来时

这样平台或工具已经基本实现了自己的功能定位，但还有些应用场景的需求覆盖不了，比如在「前后端联调」阶段可能经常发生的情况：

- 后台开发同学在自己机器上做新功能，能提供真实数据，但他自己可能经常切换服务、不稳定，导致前端请求挂掉
- 这个新功能依赖「其他服务」，其他服务不稳定，导致挂掉（很常见）

因为已经到了「联调阶段」，我们肯定大都是直接用了开发提供的真实接口真实数据了，但面对这样的「经常挂掉」，影响效率又影响心情的状况，我们能怎么办？最先想到的是重新使用原来的mock数据，不依赖开发。但可能碰到的问题如：

- 原来的mock数据可能已经「年久失修」与真实数据格式很不相同了，mock数据需要改动很多才能跑起来！可能你觉得这个不是问题，但是呢~
- 如果是一个大型的SPA应用，而你要调试的页面是「内嵌的比较深」的子页面，这个页面想跑起来、又要依赖「几个外层页面」能跑起来、也就是说外层页面的mock数据也要正确才行。
- 但这个外层页面的mock数据也很可能是年久失修跑不起来的，这个外层页面如果不是你负责、你并不了解它的业务逻辑，那么它就block了你的进度。
- 这或许就是在一个比较大型应用里、多人合作产生的典型问题，我们很着急地想让别人来配合我们做改动，但别人当时很可能就也完全没空，自己也因此就难进行下去了。

有没有办法把这种低效串行变为高效并行呢？首先联调阶段我们已经有线上真实数据，所以可以

- 把真实数据保存到本地一份，但请求不同数据有不同的controller代码，我们是否也要写一份？（要写的话，就是写了个简单的后端了）
- 但因为我们用的是「REST规范」的接口，所以能借助一些已有工具，可以用很少量代码完成这个工作

在与同事们聊过这问题后，后来同事找到了这个工具 [json-server](https://github.com/typicode/json-server)，自己看看后感觉正适合解决这个问题。它是一个能做json服务器的工具，内部依赖了一个叫 [lowdb](https://github.com/typicode/lowdb) （感觉并不low呀~）的文件数据库，提供了完整的REST API支持。可以用它做一个类似于 [firebase](https://www.firebase.com/) 的json云服务平台。我们为了解决具体项目问题、这里就先只做一个项目工具就好了。

## RESTful设计细节探讨

先了解下REST

- REST是“Representational State Transfer”的缩写，可以翻译成“表现状态转换”。REST不是一个标准，而是一种软件应用架构风格。
- 是面向资源的接口设计，抽象操作为基础的CRUD。
- 更多内容请自行查找~~

我们这里主要聊聊REST接口设计的一些细节，来自阮老师的文章 [RESTful API 设计指南](http://www.ruanyifeng.com/blog/2014/05/restful_api.html) 和 [理解RESTful架构](http://www.ruanyifeng.com/blog/2011/09/restful.html) 有不错的参考资料。里边提到：

> 在RESTful架构中，每个网址代表一种资源（resource），所以网址中不能有动词，只能有名词，而且所用的名词往往与数据库的表格名对应。一般来说，数据库中的表都是同种记录的"集合"（collection），所以API中的名词也应该使用复数。

示例如：

    GET /collection   返回资源对象的列表（数组）
    GET /collection/resource  返回单个资源对象
    POST /collection：返回新生成的资源对象
    PUT\PATCH /collection/resource：返回完整的资源对象

所以，正确的REST接口设计是否可以抽象为：

    GET /collection/id/collection/id
    例如：
    GET /zoos/ID/animals：列出某个指定动物园的所有动物

那么下面这种设计是否正确呢？（摘自实际业务代码）

    /webapi/appDomains
    /webapi/appDomains/tree
    /webapi/appDomains/systemDomains

如果按照`/collection/id/collection`这样的方式，个人觉得可能不太对，不知你怎么看？

### one more thing...

这些REST API对应的数据怎么存储呢？我们拿「文章和评论」的数据举例：

    {
      "posts": [
        { "id": 1, "title": "p1", "author": "a" },
        { "id": 2, "title": "p2", "author": "a1" }
      ],
      "comments": [
        { "id": 1, "body": "c1", "postId": 1 },
        { "id": 2, "body": "c2", "postId": 1 },
        { "id": 3, "body": "c3", "postId": 2 }
      ]
    }

可以看到，“json数据库”里文章posts和评论comments是「并列的」两个对象，comments里通过`postId`与某篇文章关联。 我们通过如下方式来请求文章列表、请求某篇文章或下边的评论：

    GET    /posts
    GET    /posts/1
    GET    /posts/1/comments

总之API设计是一方面，对应的数据如何存储如何表示，也是很重要的内容，两者协同才能搞出真正好用的东西。
