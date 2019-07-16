---
layout: post
title: "基础性能优化方法总结"
tags: [js]
author_name: warmhug
---

> 文章内容大多来自学习摘记、实践思考。

### 浏览器加载过程

浏览器从服务器接收 HTML，把 HTML 在内存中转换成 DOM 树，在转换的过程中遇到：

- CSS 或 image 请求，不会阻塞 HTML 的解析、不需要等请求的返回，当返回后，只需要把返回的内容放入到 DOM 树中对应的位置。
- JS 请求。因为 JS 有可能直接改变 DOM 树结构，为了不重新构建 DOM 树，会阻塞其他的下载和浏览器渲染。

> 参考：[网页渲染过程](http://frontendbabel.info/articles/webpage-rendering-101/)、[JavaScript Loading Priorities in Chrome](https://addyosmani.com/blog/script-priorities/)、[让我们再聊聊浏览器资源加载优化](http://www.infoq.com/cn/articles/browser-resource-loading-optimization)

### 性能优化

html 优化：

- 根据关键渲染路径、首先加载最小可用的 css js 资源，script 标签可以用 async 标记 ([分析关键渲染路径](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/analyzing-crp?hl=zh-cn))。
- 在 head 里面尽量不引入 js, CSS 下面不能有任何的 JS 标签（包括内联JS），否则会阻塞 HTML 的解析。如果必须要在头部增加内联脚本，一定要放在 CSS 标签之前 ([WebView性能、体验分析与优化](http://tech.meituan.com/WebViewPerf.html))。
- repaint：当某元素在不改变布局、发生显示/隐藏的变化时，就会发生 repaint。
- reflow：当 DOM 以一种影响到布局的方式进行操作时，会触发 reflow。
  - 例如，样式改变影响了布局、className 改变、浏览器窗口大小改变、插入一个新元素、改变文本、新增一个元素属性。当元素 reflow 时，他的子节点和其后的任何元素也会 reflow，最后所有元素进行 repaint。
  - 通过 getComputedStyle、offsetWidth、scrollWidth、clientWidth 等属性获取元素的尺寸或位置，会强行触发 reflow。
- 减少 reflow 的方法：
  - 多 style 变化可通过设置一次 className 完成。
  - 将多个元素改变都在 DOMDocumentFragment 中进行，然后用一次操作将元素片段放到 DOM 中，这样只触发一次 reflow。
  - 要在元素上做动画应该设置 `position: absolute | fixed`, 他们只会引发一个 repaint 而不是全部 reflow。
  - 通过 `scrollTo()` 进行原生滚动，性能显著，因为没有触发 reflow。

> 参考：[Minimizing browser reflow](https://developers.google.com/speed/articles/reflow)、[js-repaint-perfs](http://mathieuancelin.github.io/js-repaint-perfs/)、[js-repaint-perfs](http://leeluolee.github.io/js-repaint-perfs/#)

dom 优化：

- 删除 dom 对象时，及时删除事件监听。
- js 对象引用操作 dom 对象后，需要设置为 null 或 delete 掉。
- 尽量少访问 dom。
  - be aware that although JavaScript engines continue to get faster, the next real bottleneck is the DOM.
- Don’t load from uninitialized or deleted elements.
- 使用 innerHTML 把一大块元素替换掉，因为销毁的元素比较多、绑定着事件，会导致 GC 压力大。再插入新元素，再重新绑定事件。整体性能耗费比较大。

js 优化：

Garbage collection 是内存管理机制，在 runtime 时自动运行、不受 js 控制。如果某个变量引用是对对象的最后一个引用，那么垃圾回收会自动执行。
**全局变量不会被垃圾回收**，除非页面刷新、跳转或关闭。

函数作用域里的变量，在函数执行完毕、函数退出、没有引用时会被清理掉。
本来 Javascript 引擎能检测正在执行中的 “热” 对象并优化它、让它执行更快速；但 delete 操作会「严重改变」对象结构，导致引擎不能优化该对象。

将不需要的对象属性设置为 null 也比 delete 操作好，但这也是不必要的。

一些 tips：

- Don’t write enormous functions, as they are more difficult to optimize.
- 避免在一个数组里存在不同数据类型 (e.g. numbers, strings, undefined or true/false)
- call 比 apply 性能好！[参考](http://jsperf.com/apply-vs-call-vs-invoke)（angular1.0.6：2848行；backbone1.0.0：200行）
- `eval()`、`window.eval`、`new Function` 第一个不会被 GC 回收，后两个可以被 GC 回收。每次使用他们时都会调用脚本引擎将源代码转换成可执行代码；因此尽量避免使用。

> 参考：[writing-fast-memory-efficient-javascript](http://www.smashingmagazine.com/2012/11/05/writing-fast-memory-efficient-javascript/)、[JavaScript 启动性能瓶颈分析与解决方案](https://mp.weixin.qq.com/s?__biz=MzUxMzcxMzE5Ng==&mid=2247485658&amp;idx=1&amp;sn=a95acc8770470c50c2cd8d85a5b82e75&source=41#wechat_redirect)、[Memory Analysis 101](https://developer.chrome.com/devtools/docs/memory-analysis-101)

### 更多

- [性能为何至关重要](https://developers.google.com/web/fundamentals/performance/why-performance-matters/?hl=zh-cn)、
[JavaScript内存泄露和CSS优化](https://github.com/zhansingsong/js-leakage-patterns)、[了解 JavaScript 应用程序中的内存泄漏](http://www.ibm.com/developerworks/cn/web/wa-jsmemory/index.html)
- [js 框架性能测试](https://github.com/krausest/js-framework-benchmark)
