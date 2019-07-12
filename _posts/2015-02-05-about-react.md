---
layout: post
title: "react.js带来了什么"
tags: [js]
author_name: warmhug
---

从14年10月份，我们老大推荐我们开始关注react.js，以前自己也只是简单了解过这个库，现在看着国内外对它的关注度越来越高，那必须也是要学习下。

> 跟react.js名声相应的大概就是angular.js了，我们在14年有几个业务线都是基于angular.js在进行，遇到一个较严重问题是基于angular的组件不能达到理想中的通用性，另外angular带来许多非标准的条条框框的规则并不受人待见，同样在14年10月份angular官方公布了2.0的设计雏形、与1.x版本不兼容而且有非常大的变化，这些种种严重挫伤了我们专业前端对angular持续投入下去的决心。
>
> 另外angular的directive等东西，被react的设计者指责为「Inventing lots of new concepts (that already exist in javascript)」，事实也确实如此，因为angular的作者设计的2.x版本也将抛弃这些东西
>
> 但是，React的不鼓励使用双向绑定，这给处理表单数据和编辑表格数据带来了很多痛苦。虽然双向绑定不利于构建大型应用，但却对表单类应用来说仍然是比较方便的。

相对angular.js，基于react.js的代码更符合绝大多数专业前端的口味，也能更好的体现前端的专业性！接着，我们看看react都带来些什么呢？

- 组件化
  - build components, not templates.
  - use components to separate your concerns. With the full power of javascript, not a crippled templating language.
  - components mixin markup and display logic.
  - templates encourage a poor separation of concerns.
  - display logic and markup are inevitably tightly coupled.
  - display logic and markup are highly cohesive.
  - templates separate technologies, not concerns.

- re-render the whole app on every update.
  - the key design decision that makes react awesome.
  - when data changes,React re-renders the entire component.
  - re-rerdering on every change makes things simple.
  - No magical data bingding. No more explicit dom operations-everything is declarative.
  - 组件是被数据驱动的状态机。
- 虚dom
  - 实现高性能render、re-render的核心工具 
- unidirectional data flow（单向数据流）
- flux架构模式
  - Flux keeps things predictable. (在Flux程序和MV*程序同样在内部都有很多事情在时刻发生, 但是Flux程序的可预见性却高得多)
  - 据说：Flux is not simpler than MV*，但其实如果你实际应用flux去架构应用时，会发现它仍然是比较简单的，下面这张图就可完全形象说明。
  
  ![alt](http://hua-img.oss-cn-hangzhou.aliyuncs.com/blog/complex-Flux-data-flow.jpg)
  
  - 上图中：Dispatcher是Store回调函数的注册器，通过dispatcher调用相应action type的store回调函数。在嵌套的views（即嵌套的components）结构顶部, 一个特别的view（controller-view，注意一个页面中可以有多个controller-view、可以分别对应页面中不同的区块）监听着stores广播的事件，controller-view从stores中获取数据并且传递这些数据的到它的子代view中。
  - 关于flux的较详细的中文讲解，还可参考这篇[文章](http://xinranliu.me/2015-01-30-some-understanding-about-flux/)
  - 最后，其实flux也并没有很完美，在它基础之上的另一个方案[Reflux](http://spoike.ghost.io/deconstructing-reactjss-flux/)也可做为选择。

react.js提倡「无模板，无数据绑定」，因为作者认为“传统的模板技术”并不能真正把职责和关注点分离，而数据绑定（特别双向绑定）带来许多混乱和不清晰。

它带来的虚拟dom，让人更加意识到实际dom的性能问题，它强调 [the bottleneck is almost always the DOM mutation and not JS execution](http://facebook.github.io/react/docs/multiple-components.html#a-note-on-performance)，但是dom性能为什么会这么差呢？有没有办法解决掉呢？这个也许只有那些做浏览器的最清楚~ 这里不禁又为 Web Components 担忧，它里边的新东西如： template、Custom elements、Shadow DOM 都是在dom里边呀，会不会有什么问题呢？虽然说只是当dom变化时会有性能问题。（后来看到阮老师翻译or自己写的 [也许，DOM 不是答案](http://www.ruanyifeng.com/blog/2015/02/future-of-dom.html)，稍微感到震惊，这是大家想要抛弃dom的节奏呀！）

另外关于dom的性能问题：[这个视频也是很好的解释](http://youtu.be/1OeXsL5mr4g?t=18m23s)。从这个[react-vs-web-components](http://programmers.stackexchange.com/questions/225400/pros-and-cons-of-facebooks-react-vs-web-components-polymer)论战中摘录出一个句话：

> To sum it up: features from Web Components like templates, data binding or custom elements will have a lot of advantages over React but until the document object model itself gets significantly simplified then performance will not be one of them.

总之，dom的性能问题让人不能无视~

最后，是否想从angular等其他mv*框架转向react？别人给出一些[理由](http://www.csdn.net/article/2015-02-13/2823956-angular-vs-react)可以借鉴。
