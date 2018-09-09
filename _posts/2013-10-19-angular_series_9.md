---
layout: post
title: "angular.js系列九：测试"
tags: [js]
author_name: warmhug
---

### unit test   ###

While this may seem obvious it usually is very difficult to be able to call an individual function on a typical project. The reason is that the developers often mix concerns, and they end up with a piece of code which does everything. It reads the data from XHR, it sorts it and then it manipulates the DOM.

With Angular we try to make it easy for you to do the right thing, and so we provide dependency injection for your XHR (which you can mock out) and we created abstraction which allow you to sort your model without having to resort to manipulating the DOM. So that in the end, it is easy to write a sort function which sorts some data, so that your test can create a data set, apply the function, and assert that the resulting model is in the correct order. The test does not have to wait for XHR, or create the right kind of DOM, or assert that your function has mutated the DOM in the right way.

http://docs.angularjs.org/guide/dev_guide.unit-testing

### end-to-end test ###

### 示例 ###

一般需要测试的是controller中的业务逻辑，由于不涉及到dom操作，测试起来便简单不少。

示例随后跟上 :）