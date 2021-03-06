---
layout: post
title: "这可能是最后一次聊angularJS"
tags: [js]
author_name: warmhug
---

> 注：尽量从客观实际的情况出发，聊的也多是它里边“不好”的东西，但并非持有偏见（反而实是真爱）。

近一年来，angularJS遭受到前所未有的质疑与否定，与此同时，reactJS也发展到了如日中天的地步。这过程就像潮起潮落，荡出许多精彩的浪花，同时也推动了前端“这艘船”加速向前进。

angularJS问题出在什么地方？觉得可能最主要有两点：

1). 不太符合前端同学的口味，直接导致前端同学抵触情绪

  - 依赖注入、service、许多显得多余概念等，借鉴自后端
  - 自创的module，跟前端主流模块化想法不同
  - “脏检查”机制，一开始给人的印象就是性能低下（虽然可以优化的很好）

2). “组件化”的设计与实现并不彻底、也不是很优雅

  - directive很复杂，各种配置项、各种最佳实践
  - scope的几种类型、transclude、编译的不同阶段的区别等等带来的复杂度

想当初到处是称赞ng的时候，大家没有看到这些缺点吗？确实看到了，只是当时没有更好的方案解决这些问题。后来reactJS提出了更好的方案，把“组件化”设计与实现的问题解决的更好了。除了react更好的解决了组件化问题，immutable更好的解决了diff性能问题，有不少类flux更好解决了数据层的问题，GraphQL试图想更好地解决Rest遇到的问题... **所以，这么看来，当你看到问题、或哪里感觉不爽时，就要相信肯定会有更好的能解决这问题的东西出来！** 也相信目前的各种方案不是“终极方案”，因为仍然还会感到有问题、还是没那么爽。

angularJS另外一个不受前端圈待见的一点，我觉得是 **“离node.js太远”** 。这点react就做的非常好了：直接用npm做包管理；跟基于node的各种web框架几乎做到无缝结合；直接支持node服务端渲染；轻松实现Isomorphic JavaScript。但稍微提醒下，现在醉心于玩node.js的大多都是前端圈子那些人，像主流Java/.net后端没几人玩这个。这个时候react和node贴的太近的方案，更多是前端同学自认为的完美、自嗨，主流后端同学是否真的喜欢，应该还要打个问号吧（个人看法）。当然这个状态以后应该也会有改变，因为

> 随着es6/7对js语法层面的完善、TypeScript等“类型化”js的流行，js未来还是能做到类似后端语言编译检查、便捷的错误排查等功能，那时候js应该真能“堪当大任”，会成为比较舒服的“全栈化”语言吧。

其实能够“背靠npm，能轻松发模块”这点，在前端圈子里非常重要，一是能很简单地做到多人合作、又能互不干扰，还有嘛模块发的多至少也算是影响力重要体现。这点angular同样不能很好做到，稍微注意下就知道有几个人会是从npm上下载angular模块的呢？GitHub上angular模块的特点是什么？大多都是些功能模块如ng-file-upload / ui-router / ngStorage等，或者是一整套的ui模块如angular-ui / angular-strap，但很少会有独立的细小的ui组件如：dropdown / pagination，而现实中正是最后这些细小的ui组件能够让大家轻松合作、相对容易快速地一起开发一个大ui组件集合出来。这样在公司里就更容易体现出团队合作等诸多好处，对内对外也都好看，大家懂得。angular为什么不能这样呢？个人感觉一个主要原因就是angular自创的module机制，并非独立的module，会产生命名冲突等问题，不利于大范围的团队合作。

另外也还有更实际的困难，因为angular学习曲线陡峭、学习成本高(全面深入学习时)，所以在其上做出优秀或者质量较好组件的难度会更大。大多数接触angular不是很久的同学写组件估计会感觉上手困难、自然也难以写的很好。再看看GitHub上那么多angular的ui组件或功能组件库，个人感觉写的“最好最地道最ng的”大概也只是官方的[ui-bootstrap](https://github.com/angular-ui/bootstrap)了。

吐槽了这么多，不管怎样，如果曾经用过angular，虽知其缺点但却是真爱，或者非真爱但也并非对之深恶痛绝，或者如果在想未来前端技术可能的变化点里有什么，那么可以继续关注下angular的[官方博客](https://blog.angularjs.org/)，他们正在进行的angular2的开发，当然也是基于目前所有已出现的前端主流新技术的基础上的思考下进行的，应该也会给你点益处。

因为有了更先进生产力react，大家终于脱离了angularJS这“落后生产力”了，这也应该是自己最后一次聊angularJS了(v1.x版本哈)，总之感谢经历了几年里围绕这个发生的许多故事，它已“老”去，但但愿大家不要忘记它也是前端发展中至关重要的一步。

最后发个来自后端Java开发同学的声音：

> angular在业务里简单的用起来，其实还是挺舒服的！
