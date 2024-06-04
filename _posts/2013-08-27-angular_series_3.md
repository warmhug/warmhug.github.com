---
layout: post
title: "angular.js系列三：ng-class等api应用"
tags: [js]
author_name: warmhug
---

### ng-class应用详解 

官方demo指出：ng-class="{expression}"，所以，ng-class的使用关键就是要懂得怎么写这个“表达式”[expression](http://docs.angularjs.org/guide/expression)。下边列出书写表达式的易错地方：

- expression不支持“三元运算符（即v？x：y）”，可以用`&&`和`||`模拟实现，但1.1.5版本会支持。


这里列出ng-class值的三种写法：

	假如：isSel == $index  -> true
	ng-class="{true: 'sel', false: 'notsel'}[isSel == $index]"

	ng-class="{'sel': isSel == $index, 'notsel': isSel != $index, 'red': isRed}"

	模拟三元运算符
	ng-class="isSel && 'sel' || !isSel && 'notsel'"

一般的操作，直接在class属性中写表达式即可：
{% raw  %}
	<div class="box {{ isSel && 'sel' || 'notsel'}}">
{% endraw  %}

**注意**：scope上只能附加 数据 和 操作数据的方法，不要附加view上的东西（例如具体的className）！


实际业务中，view上一般都会有类似这样需求：

	if(condition){ 
	  '<h2></h2>'
	}

但是目前angular的稳定版不支持ng-if语句，所以只能用ng-switch或filter模拟或用一个ng-if的ui扩展。但ng-switch会引入多余的标签；filter实现则需要多写个filter，而且筛选后插入的一般是html，还涉及到html的安全性及其上边的数据绑定能否生效等问题。

最后好消息：1.1.5版本除了支持 三元运算符 也将支持 ng-if


### form、ng-form、ng-model 
表单在实际应用中非常常见，虽然在ajax应用程序里，表单提交都是走的异步接口，input外边再套个form好像也作用不大，但form的submit能天然支持键盘enter等，所以为了便捷form还是很必要的。

通常的form里不能再嵌套form，angular提供了ng-form别名，允许form嵌套，这样一个大form里可以有独立的子form、可对其做独立的合法性验证。

在验证form时，会依据结果附加这么几种状态样式名：ng-valid、ng-invalid、ng-pristine、ng-dirty，写入自己想要的样式，来表明验证状态变化。form指令也提供了ngSubmit来做提交，但要注意form里的input个数和html规定的相应的提交触发规则，防止表单重复提交。

详细：[Forms-guide](http://docs.angularjs.org/guide/forms)

form里通常会涉及到用户输入等交互，此处便能很明显体现出 “双向绑定” 的特性。文档中指出：理解双向绑定的关键directive是ngModel，它提供了双向绑定功能，另外也提供了$setValidity等api来增强其他directive的行为。关于ngModel的介绍，以及与它相关的各类input指令的用法等，参考[此处入口](http://docs.angularjs.org/api/ng.directive:ngModel)。接着会专门有一篇文对“双向绑定”这一许多库或框架都提供的特性做专门对比介绍。


### 其他api应用
  
由于angular是将浏览器解析好的dom作为模板输入再进行解析的，而dom上一般都会附有
{% raw  %}{{ expression }}{% endraw  %}之类的模板model表达式，在解析这些标记前必然会有一段等待时间，网络慢时，用户就可能看到这些原始标记或页面闪烁下。怎么解决呢？    
用 ngCloak 指令；或者用 ngBind 代替{% raw  %}{{ expression }}{% endraw  %}，因为ngBind只作为html元素的属性，对用户不可见。

ng-show、ng-hide、ng-switch、ng-if、ng- ~event等很常用，也比较简单，另外一个ngPluralize这个看起来也挺酷，但感觉实际用处不多。

ng-repeat细节内容比较多，很常用，应该需要重点学习使用。

另外提供了currency、date、json等多种内置filter，有些还是有用处的，但更多时候需要写自己需要的filter。

内置的各种service，像$http、$rootScope、$timeout、$window、$q、$compile等都是很常用的，需要了解它们的用法。

Types分类下的Module、Attributes、Scope、FormController、NgModelController都算是很“基础”的东西，同样要根据需要不同程度的了解掌握。

global APIs下就是常用的各种公共方法了。在处理不安全的html内容时，$sanitize、$sce提供了一些方法和示例。

其他的module，像ngRoute、ngResource业务中几乎必需要使用；ngMock做测试的mock接口使用，AUTO下的$injector、$provide则相对“底层”些，使用时有其他相应简单的代替方案。 


### 总结： 

本文主要介绍了ng-class的使用，顺带介绍了其他api的简要用处及其重要性，多多查阅[api](http://docs.angularjs.org/api/)列表，对解决使用中遇到的问题是很有帮助的。


### 引用： 

- [http://stackoverflow.com/questions/7792652/what-is-the-best-way-to-conditionally-apply-a-class-with-angularjs](http://stackoverflow.com/questions/7792652/what-is-the-best-way-to-conditionally-apply-a-class-with-angularjs)
- [http://docs.angularjs.org/api/](http://docs.angularjs.org/api/)




