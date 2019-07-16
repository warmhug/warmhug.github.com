---
layout: post
title: "Rest 和它的革新者们"
tags: [js]
author_name: warmhug
---

去年的这个时候，前端社区里 React.js 势头开始超越 Angular.js，现在 React 终于变成了前端最火的库。而近几个月同样发生着另外一个有趣又类似的事：在后端被广泛使用的 Rest (RESTful API)，开始受到各种质疑、特别是声称解决或替代 Rest 的方案如 [GraphQL](http://facebook.github.io/graphql/)、[Falcor](http://netflix.github.io/falcor/) 的出现并得到越来越多的关注，好像革命大旗举了起来。

Rest 有什么问题？大家普遍提到的有这些：

### 一：资源粒度

> 通过一个请求加载所有的信息同时使得我们的 Rest 资源保持隔离之间的冲突。 ref: [GraphQL](http://www.infoq.com/cn/news/2015/10/graphql-your-schema)

Rest 的资源是拆分的“比较细的”，但应用里是想通过一个请求直接拿到尽可能多的数据，但对于 Rest 一次请求只能拿到某一个资源，这样就产生冲突了。

但对于这个问题，资源的 embeded 其实是可以解决的。目前对于那些有类似 “外键” 关联的资源（如一个学生数据库表里关联着老师的id），客户端发一个学生资源的请求、带上关联老师详情的 query 信息，就能把老师的详细信息关联查出来，一并发送给客户端。

### 二：实际场景

> 常见的图片瀑布流应用（无尽列表），随着用户鼠标滚动，要不停的发送请求，显示新的图片并获得相应的图片详细信息。

这种场景下，如果设计不当，例如在用户滚动的时候，才去加载需要的细粒度资源。那么假如获取一张图片的详细信息时，就需要发送至少 5 个资源请求，用户随便拖动下滚动条，就可能需要显示10张图片，这样一下子至少要发送 `10*5` 个资源请求，很明显这是不能忍的也是不对的。

这类场景正确的做法是只需要发送一次请求，返回一个列表数据，并在里边包含各种子资源即可。只是如果资源粒度比较细的话，子资源需要被 embed 进来就需要拼接不少参数，比较麻烦。

举个实例：

```js
// 资源列表如：
[{
  id: 1
  name: "card",
  boxshot: "http://xx",
  rating: 5,
  bookmark: 2343,
  director: "david",
  // more fields
},
...
]

// Rest 请求列表资源
/list?rowOffset=0&rowSize=5&colOffset=5&colSize=15&titleprops=name,boxshot

// RPC 风格：
/list?pageSize=10x15&titleprops=name,boxshot

// 请求单个资源部分信息：
/list/123?props=name,rating,bookmark
```

以上可以看出，Rest 请求列表资源时，可能需要加很多参数，还不如 RPC 风格来的简单。这也大概正是 [Falcor](http://netflix.github.io/falcor/) 作者要解决的问题，把 RPC 里的优点拿过来，更简单的组合资源。

> Falcor 作者提到的 Rest 的设计初衷（[视频](https://youtu.be/hOE6nVVr14c?t=5m48s)）
>
> The REST interface is designed to be efficient for large-grain hypermedia data transfer, optimizing for the common case of the Web, but resulting in an interface that is not optimal for other forms of architectural interaction.

### 三：Rest API 的设计并不容易

一开始我们做系分时，可能比较容易划分出有哪些资源，对资源粒度划分也可能没那么难。但把这些资源组合或规划起来、对外提供 API 时，可能就没那么容易了，会有让人纠结的地方。如：

- 资源与子资源的组合和划分。有一些资源有明确的父子关系，但很多资源并没有，很多资源可能是平级关系、但需要互相联系起来。具体例子如：

```sh
GET /cars/711/drivers/
Returns a list of drivers for car 711

GET /cars/711/drivers/4
Returns driver #4 for car 711
```

- 一些动作类资源的恰当处理，常见的如登陆、验证等。

这些问题看起来简单，实际去设计时、却很容易做不好。

## 那么 Rest 自身能否解决以上问题

一开始 Rest 的出现，是为了解决 Rpc 的问题。Rpc 方式下，应用只有一个端点(endpoint)，导致紧耦合、不易缓存。

> Rpc 和 Rest 的基本区别：
>
> SOAP Web API采用RPC(面向方法Remote Procedure Call)风格，它采用面向功能的架构，所以在设计之初首先需要考虑的是提供怎样的功能。
>
> RESTful Web API采用ROA(面向资源Resouce Oriented Architecture)架构，所以在设计之初首先需要考虑的是有哪些资源可供操作。

引用下 [richardson rest 模型](http://martinfowler.com/articles/richardsonMaturityModel.html):

- Level 1 Resources （解决了 Rpc 问题）
- Level 2 Http verbs （使用 http verbs 来对各种资源进行 crud 操作，使应用接口更加的统一）
- Level 3 Hypermedia Controls
  - level 3 带来了 service discoverablility 和 self-documenting。举例就像访问了某一个最顶层资源，它会返回一个它的子资源列表地址；再访问其中某个子资源，这个子资源又返回它的子资源或关联资源的地址；依次下去甚至能发现整个应用的所有资源地址。实例如 [GitHub-API](https://api.github.com/)。

文中作者只提到了第三层级。而上边我们提到过资源的父子资源的 embeded 试想可能的话，让所有资源都能自由的互相 embeded 或者自由的做关联，那就是更进了一步，也差不多能解决以上提到的问题。但即便做到如此，Rest 也还是有其让人觉得不爽的地方。

GraphQL / Falcor 这类方案，都是宣称解决了 Rest 的一些问题，但其实也只是特定的场景，而它们真的是更上一层楼？恐怕也不见得，待以后实践证明吧。
