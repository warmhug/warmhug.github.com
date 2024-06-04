---
layout: post
title: "函数式编程入门"
tags: [js]
author_name: warmhug
---

> 文章内容大多来自学习摘记、实践思考。

对象是面向对象的第一型，那么函数式编程也是一样，函数是函数式编程的第一型。

在纯粹函数式程式语言中，你不是像命令式语言那样命令电脑「要做什么」，而是通过用函数来描述出问题「是什么」。
递回在 Haskell 中非常重要。命令式语言要求你提供求解的步骤，Haskell 则倾向于让你提供问题的描述。这便是 Haskell 没有 while 或 for 循环的原因，递回是我们的替代方案。

在面向对象编程中，我们把对象传来传去，那在函数式编程中，我们要做的是把函数传来传去，我们把他叫做 **高阶函数**
在函数式编程中，函数是基本单位，是第一型，他几乎被用作一切，包括最简单的计算，甚至连变量都被计算所取代。在函数式编程中，变量只是一个名称，而不是一个存储单元。

### Persistent data structure

Immutable data structures：当一个对象被创建之后、就永远不会变了，如果需要改变、就只能创一个新的。

```js
const obj = { text: 'hello' };
obj.text = 'world' // 这样不行，因为改变了 obj 这个对象
const newObj = { ...obj, text: 'world' }; // 必须要创建一个新对象
```

一般为了实现不可变性，最直接的做法可能是直接拷贝对象，但因为效率太低不可行，而是利用了 structural sharing，这样就可以最小化的拷贝对象的一部分。拷贝的是哪一部分呢？如图：

![wiki 图示](https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Purely_functional_tree_after.svg/876px-Purely_functional_tree_after.svg.png)

这样嵌套的深层对象，只需拷贝 f、g、d 这一个链，其他的结构共享，这样创建了一个新对象，就达到了 immutable 的目的，而且效率和内存占用都比较合理。但注意一个问题[Circular references](https://github.com/facebook/immutable-js/issues/259)，这个问题在 Haskell 等语言中在语言层面被解决了，但在 js 中似乎难以解决。

> Structural sharing is a powerful concept, and is what enables Clojure persistent data structures to achieve O(log n) performance on operations that would otherwise require full O(n) copies of a data structure.

## 更多

- [Immutability in JavaScript](http://www.sitepoint.com/immutability-javascript/)
- [Persistent_data_structure](https://en.wikipedia.org/wiki/Persistent_data_structure)
- [React.js Conf 2015 - Immutable Data and React](https://www.youtube.com/watch?v=I7IdS-PbEgI)
- [使用JavaScript实现“真·函数式编程”](http://jimliu.net/2015/10/21/real-functional-programming-in-javascript-1/)
