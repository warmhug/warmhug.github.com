---
layout: post
title: "angular.js系列八：源码简析"
tags: [js]
author_name: warmhug
---

(注：主要依据1.0.7版本)

源码总的结构看起来挺朴实简洁：外围是一个匿名函数，里边就直接放置各种需要的变量定义、函数定义、调用执行语句等 ‘似乎’ 很容易看懂的代码。的确，前900行代码只是些很常见的工具方法、兼容处理、后边的JQLite部分多数类似jQuery，只看看这些会感觉是挺简单的。但这些‘简单易懂’的代码只占了总量的七分之一，其他代码写法上也不麻烦，但如果不懂作者意图、那么代码含义也就难以理解了。

个人感觉angular比较'鸡肋'的一点首先就是在源码初始化时，执行了许多代码，其运行过程大致是这样：

- 1先定义必要的变量，各种工具方法
- 代码里`forEach(..)`调用来初始化各种对象，包括JQLite
- 后边的`bindJQuery()`、`publishExternalAPI(angular)`公开api，初始化各种内置directive、service的Provider等
- `angularInit(document, bootstrap);` 启动app
- 检查并缓存各种需要的对象、html编译解析等一系列过程


源码中有许多对象都是比较“大”的，还有不少cache对象，像`instanceCache`、`providerCache`等，这个对内存的占用是否过大了？但这些对象也都是有用的，那么会不会有更好的实现方式？个人觉得应用中没有用到的一些对象，干脆就不要初始化，这样是不是节省一点呢。

调试源码会看到：在配置各种directive、service时， `instanceInjector.invoke(provider.$get, provider)`、`function annotate(fn)` 这两段执行的次数是很多的，这是由于其 “依赖注入(DI)” 的实现模式，所以调用和依赖解析的地方有很多。还是由于DI，某个模块依赖的模块、还可能会依赖其他的模块，这种一层层的依赖，解析起来就感觉特别的耗费运行时间（ps:自己调试起来就不禁难受：`function invoke(fn, self, locals)`、`function getService(serviceName)`函数执行了n多次...何时能把这层层依赖解析完毕呀？！）。

经过上段介绍的“漫长”的各种依赖解析、并缓存进相应的cache对象后，终于能走到`$CompileProvider`里了，当执行里边的`function compile($compileNodes, transcludeFn, maxPriority)`就开始了对html模板的“编译”过程，这里收集各种directives，收集到我们代码里自己定义的directive，再解析其依赖，然后终于进入我们自己写的directive代码里，在模板被编译后，到达link阶段，之后会执行我们自己写的逻辑。

link阶段执行完毕，接着执行`$rootScope.$digest()`，而这个`Scope.prototype`上的`$digest`函数 正是进行 “dirty checking” 的地方。代码并不麻烦，就是把注册了watch的对象的现有值与上一次值的对比，来判断是否执行相应的watch回调函数等。这里有个对angular和backbone处理数据绑定的方式对比的详细解释：[dirty-checking (angular) vs change listeners (KO, backbone)](http://stackoverflow.com/questions/9682092/databinding-in-angularjs)，作为学习了解很有价值。
