---
layout: post
title: "angular.js系列六：数据绑定"
tags: [js]
author_name: warmhug
---

双向数据绑定，首先涉及到数据model，先[回顾](http://docs.angularjs.org/guide/dev_guide.mvc.understanding_model)下什么数据才算model，如何创建model等。从中可以看出，model实际就是angular Scope对象上的属性，所以理解model，也就是主要理解[Scope](http://docs.angularjs.org/guide/scope)了。

controller中的代码基本代表了项目的业务逻辑，但在里边我们看到的都是对数据的各种操作。而model -> view 或 view -> model之间变化所需的事件监听与处理的代码都是透明的，框架本身都替我们做好了，少数必须要自己书写的操作dom的代码也都放在directive中了。这意味着什么？数据绑定很重要，数据操作就是核心。

“双向绑定” 是数据绑定内容的一部分，官方文档中 [模板数据绑定](http://docs.angularjs.org/guide/dev_guide.templates.databinding) 这里简要介绍了angular模板的不同之处及其数据绑定方式。“双向” 的意义，个人觉得可以阐述如此：用户点击元素、填写表单等view上的行为，能直接引起相应的数据变化，即model改变；反之，像服务端推送过来的数据使得model改变，也能直接引起相应view上的显示改变。这种 “自动化” 的相互反馈，即为双向绑定。很显然，解释双向绑定，需要用户操作的配合，用户操作大多是操作form control元素，而[ngModel](http://docs.angularjs.org/api/ng.directive:ngModel)正是主要作用在input等control元素上的，所以为了加深印象，引用官方一段关于“双向绑定”和ngModel的相关介绍：

> The key directive in understanding two-way data-binding is ngModel. The ngModel directive provides the two-way data-binding by synchronizing the model to the view, as well as view to the model. In addition it provides an API for other directives to augment its behavior.

有关ngModel的详细内容还有很多，不再具体介绍。

同一数据可能被绑定在很多个地方，angular是如何检查这些数据变化并全都做出相应改变？按官方说法是采用 **Dirty checking** 机制。这个过程大致是怎么样的，我们简单描述下：  

依据Scope生命周期（Scope Life Cycle）描述，dirty check主要发生在 “变化监测(Mutation observation)” 阶段。angular会在root scope执行一个$digest周期，这将会传播到所有child scope中。在$digest周期中，所有注册了$watch的表达式或者function都会被检查，判断model是否发生了改变。

很显然，在$digest周期，需要检查的表达式过多时，会出现性能问题。不过dirty check是异步的，controller中的常见赋值如： $scope.username="angular" 将不会 **马上**导致一个$watch被通知，$watch的通知将会延迟到$digest阶段。这就把多个model更新联合到一个$watch通知中，这也保证了在$watch通知的过程中，没有其他$watch在执行。如果一个$watch改变了model的值，那么它将会强制增加一个$digest周期。

到这里就可以联系到`Object.observe()`这个东西了，这里展示一个[demo](https://github.com/warmhug/diy-classic/blob/master/data%20binding/Object.observe.html)，注意它和angular的数据绑定的相似之处，可以看到它应该即将被浏览器正式支持，而它也正解决了这个dirty checking的效率问题。

对比之下，backbone.js只实现了单向的数据绑定，当然也有[backbone.stickit](https://github.com/NYTimes/backbone.stickit?source=cc)类似这样的双向绑定的插件提供，但使用起来总会觉得有一点别扭与不便，而不如angular这类mvvm框架在这方面来的自然方便。
