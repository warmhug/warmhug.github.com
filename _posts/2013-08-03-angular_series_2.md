---
layout: post
title: "angular.js系列二：应用起步"
tags: [js]
author_name: warmhug
---

上篇文章里提到过，本系列文章，主要从应用角度出发，来讲解angular的一些东西。上篇文章指出一些入门的学习地址，假如你看过那些资料，那么应该会碰到一些疑问点，不要着急再找资料寻求答案，因为有更好更有效的方法 -- 从实际应用出发，碰到问题、思考问题、寻求答案！

如何搭建一个能在**IE7\8**和其他现代浏览器中跑起来的demo呢？官网Developer Guide这里有教程：[bootstrap](http://docs.angularjs.org/guide/bootstrap)和[Internet Explorer Compatibility](http://docs.angularjs.org/guide/ie)。教程已经相当详细，但这两处本来该合并到一起或者挨着写的，可却分散在不同地方，给人使用上带来些不便。

为兼容IE7、8增加了不少设置，这里再对之整理下：

- `<html xmlns:ng="http://angularjs.org" id="ng-app" ng-app="optionalModuleName">`即ng-app需要加上id="ng-app"
- 引入json2.js或json3.js
- body里有任何类似`<ng-include></ng-include>`的非html本身就有的元素标签，需要在script中加入document.createElement('ng-include');之类的代码创建相应元素，来使得ie能正确渲染这些自定义元素

angular提出了许多[概念](http://docs.angularjs.org/guide/concepts)，大概也正是这些概念打动了许多人的心。我们为了在项目实际应用，需要从这些概念中抽出基础的、实质的内容，来深入理解学习才行。

- [expression](http://docs.angularjs.org/guide/expression)是基础。需要记住‘表达式’该如何写，它不同于js表达式的地方是哪里，它为什么不推荐有流程控制语句。
- [di](http://docs.angularjs.org/guide/di)依赖注入在angular中到处都是，service、filter、provider等都是通过di来使用。这里要注意下依赖的声明方式：推断式、$inject声明式、内联式（应用于实际项目中）。
- [directive](http://docs.angularjs.org/guide/directive)是主要内容。这里教程内容很多，同时也好多地方难理解，主要是它掺杂了一些scope上的东西。简短版本的directive容易些，但长版本（完整版本）的才真正有实际用处，即Directive Definition Object里各个配置项的意义都要理解透彻。
- [scope](http://docs.angularjs.org/guide/scope)同样是主要内容。scope是controllers和directives之间的胶合剂。scope上绑定的属性是唯一的数据源。scope其提供的一些事件方法（$emit $broadcast等）是controllers之间或directives之间的通信工具。scope提供的$watch $apply等方法用于监控数据变化和、把angular环境之外（例如jquery插件里）的数据纳入监控管理中。

这里对**scope**进行一些总结：  
1、scope是controller和view之间的‘粘合剂’。controllers和directives都有引用scope，但彼此互不了解，也就是controllers和view之间互不了解，这样就提高了controller的可测性。  
2、scope上的属性是被用于渲染view的数据，并且是view上数据的唯一来源（single source-of-truth）。  
3、scope的结构：root scope → child scopes → child scopes → ... 产生类似于dom的树结构，子scope属性能继承父scope属性（isolate scope需要额外声明才可继承）  
4、获取某个元素的scope：angular.element(ele).scope(); (只用于调试)  
5、Scopes can propagate events in similar fashion to DOM events. The event can be broadcasted to the scope children or emitted to scope parents.  
6、scope的生命周期：创建（通过$injector）-- Watcher registration -- Model mutation（重点，angular之外的环境导致的model改变，需要用$apply()） -- Mutation observation -- Scope destruction（不使用时最好销毁掉）

以上介绍了实际项目中需要用到的主要内容，其中的不少具体细节需要你自行去了解。接下来的系列文章我们讨论些项目应用中碰到的具体问题的分析，包括ng-class、directive通信等复杂用法、代码组织建议等内容，敬请期待：）
