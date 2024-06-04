---
layout: post
title: "angular.js系列五：组件写法与组件间的通信"
tags: [js]
author_name: warmhug
---

这里说的“模块/组件”，主要指由directive指令构成的“功能组件”或页面逻辑块组件。比如 “对话框”、“搜索框” 、“列表”等。这些模块的怎么书写主要依据业务逻辑来定，但模块之间如何通信呢？哪些通信方式是好的呢？本文主要讨论这个问题。

本文需要你知道一些预备知识：[directive](http://docs.angularjs.org/guide/directive)、[Scopes](http://docs.angularjs.org/guide/scope)、[Understanding-Scopes](https://github.com/angular/angular.js/wiki/Understanding-Scopes)、[Scope api](http://docs.angularjs.org/api/ng.$rootScope.Scope)，这几个链接的内容量比较大，但还是很需要理解清楚的。重点理解directive的各种写法和配置的不同作用，scope（normal / isolate scope）的结构和事件写法（$broadcast、$emit、$on等），另外可参考本系列2 [应用起步](http://warmhug.github.io/2013/08/03/angular_series_2.html)中最后对scope进行一些总结。

由于scope知识点比较基础比较重要，这里从[Understanding-Scopes](https://github.com/angular/angular.js/wiki/Understanding-Scopes)文章中摘录出总结的部分：

（是/否）原型继承的scope创建方式：

- The following create new scopes, and inherit prototypically: ng-repeat, ng-include, ng-switch, ng-view, ng-controller, directive with `scope: true`, directive with `transclude: true`.
- The following creates a new scope which does not inherit prototypically: directive with `scope: { ... }`. This creates an "isolate" scope instead.

Note, by default, directives do not create new scope -- i.e., the default is `scope: false`.

四种类型的scopes:

- normal prototypal scope inheritance -- ng-include, ng-switch, ng-controller, directive with `scope: true`
- normal prototypal scope inheritance with a copy/assignment -- ng-repeat. Each iteration of ng-repeat creates a new child scope, and that new child scope always gets a new property.
- isolate scope -- directive with `scope: {...}`. This one is not prototypal, but '=', '@', and '&' provide a mechanism to access parent scope properties, via attributes.
- transcluded scope -- directive with `transclude: true`. This one is also normal prototypal scope inheritance, but it is also a sibling of any isolate scope.


组件怎么写？

- angular文档[首页](http://angularjs.org/)的“Create Components”部分的demo就是个好例子。这里再提供个 [对话框](https://github.com/warmhug/warmhug.github.com/blob/ecbaf3fee79e9f0f0bb38b3a9604f8ac8f58c2d1/demo/angular-sty/series_5/isolate-scope-dialog.html) 组件的demo，侧重于演示directive上的scope的配置（此示例创建了个isolate scope，即此对话框是个“可复用”的组件）。
- 更复杂的还有涉及到多个形成isolate scope的组件互相嵌套，怎么传递数据的问题等。
- 再来个[demo](https://github.com/warmhug/warmhug.github.com/blob/ecbaf3fee79e9f0f0bb38b3a9604f8ac8f58c2d1/demo/angular-sty/series_5/directive-nested-2waybind.html)，主要演示$apply的使用，绑定数据为primitive类型(e.g., number, string, boolean)即代码中的`$scope.prim = 'xxx';`双向绑定无效的情形。

- 组件之间如何通信？先看[demo](https://github.com/warmhug/warmhug.github.com/blob/ecbaf3fee79e9f0f0bb38b3a9604f8ac8f58c2d1/demo/angular-sty/series_5/directive-communicate.html)，注意demo上的“注释”部分！这里只做下最终总结：

    - 嵌套directive中，子directive能配置require父directive，这样能获取到父directive上controller里暴露的方法。
    - 原型继承的scope，即不是这种`scope: { ... }`，子scope能直接使用父scope上的数据
    - 封闭scope，即这种`scope: { ... }`，可以通过scope上配置暴露信息，另注意demo中提到的问题
    - 以上两种类型scope，都可以通过注入$rootScope，然后$broadcast来广播消息
    - 总的来说，组件之间的通信，就是采用了 “消息模式” ，这种模式本身就对系统解耦很有帮助，在组件里使用就很自然了。