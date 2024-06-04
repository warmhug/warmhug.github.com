---
layout: post
title: "mv(c/p/vm)再比较"
tags: [js]
author_name: warmhug
---

前边一系列文章讲述了在使用angularJS过程中遇到的问题及相应的解决方法，算是作为对mvvm模式的实际应用。在这之前也对backbone.js做了些demo应用，也看过一些用backbone做的项目，大家都称它为mvp模式，不过它相对mvvm之类的框架来说，复杂度小了不少。mvp/mvvm都是mvc模式的变种，看到某人的一篇文章，它表示[mvc不是一种设计模式](http://damoqiongqiu.iteye.com/blog/1949256)：GOF的23种设计模式里面没有mvc，其实是其它三个经典的设计模式的演变：观察者模式(Observer)(Pub/Sub), 策略模式(Strategy)和组合模式(Composite)。因此MVC算是多种设计模式组合成的一种架构模式吧。

模式需要谈，但更重要问题的是：我们为什么要用这个模式？有没有必要使用？哪些类型的项目适合使用，哪些又不适合？正确的选型的前提是，对这些模式及其相应库的深刻理解，对项目需求的详细思考分析。不过反过来说，“正确的”大多都是在“错误的”基础上积累与反思后才形成的，所以感觉可以用，只要有充裕时间有兴趣，那就用用看吧，不要怕错误~~

许多人已经提出关于mvc及其变种的理解比较，本文想从另一个角度去理解、去解释，以后再说~~