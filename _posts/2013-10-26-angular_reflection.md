---
layout: post
title: "反思angular"
tags: [js]
author_name: warmhug
---
回过头来再看angular，我们做个总结，先说说它的优秀之处：

- 它推崇并支持了“声明式的动态html”（declaring dynamic views）、扩展了html标记和行为
- 通过免去一般的“dom操作”、“事件注册监听”、“input验证”等，来减少开发者工作量，而能更好专注于业务逻辑，即业务代码的controller里主要是操作model数据
- 它带来的东西：“数据、业务逻辑、表现层的分离(mvc ? mvvm)”、“数据绑定”、“Services”、“Dependency Injection”、“An extensible HTML compiler”、“Ease of Testing”


### 不足之处 ###

angular为什么采用这种模板渲染方式，为什么这么组织代码？从这里大概能看出些道理：  
The Zen of Angular  
Angular is built around the belief that declarative code is better than imperative when it comes to building UIs and wiring software components together, while imperative code is excellent for expressing business logic.


http://hueypetersen.com/posts/2013/06/17/angular_is_slow/

http://bguiz.com/post/57373805814/accessors-vs-dirty-checking-in-javascript-frameworks

http://www.quora.com/Client-side-MVC/Is-Angular-js-or-Ember-js-the-better-choice-for-Javascript-frameworks#


- String-based templates（Handlebars 、mustache）能够在服务端进行‘预编译’，直接返回函数给浏览器；app变大时，更容易拆分、进行模块懒加载。angular不行。
- angular需要在启动时，遍历dom，app越大，启动越慢
- expressions每次改变时都需要重新计算，dom刷新，dom绑定越多，运行越慢。
- Because Ember.js only allows you to bind to properties, we will very easily be able to take advantage of the performance benefits of ECMAScript 6 features like [Object.observes](http://wiki.ecmascript.org/doku.php?id=harmony:observe). Because Angular invented their own subset of JavaScript with a custom [parser](https://github.com/angular/angular.js/blob/master/src/ng/parse.js), it would be difficult to allow browsers to optimize this in native code.
- dirty checking消耗性能
- ember更容易整合‘第三方库’和‘异步’的东西 ？

