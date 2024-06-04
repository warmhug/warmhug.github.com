---
layout: post
title: "再谈innerHTML"
tags: [js]
author_name: warmhug
---

这是一个很基础的东西，但发现自己以及别人的不少代码里仍旧有对此运用不适当的地方。这个属性最初是个由浏览器厂商实现的非标准属性，如今已经被whatwg和w3c作为html5标准属性。老版本的浏览器对此属性的渲染方法也不尽相同。

先列出经典的方法对比：innerHTML vs "W3C DOM methods":  
  
- It's faster than DOM methods. [http://www.quirksmode.org/dom/innerhtml.html](http://www.quirksmode.org/dom/innerhtml.html "quirksmode")
- It's less verbose than DOM methods.
- It allows you to take arbitrary chunks of markup and drop them into a document without having to parse them.

总结起来：innerHTML相对"W3C DOM methods"，速度更快，写法更简洁，更灵活。


有篇老文章总结了一些innerHTML的问题：

- 当 HTML 字符串包含一个标记为 defer 的 script 标签（`<script defer>…</script>`）时，如 innerHTML 属性处理不当，在 Internet Explorer 上会引起脚本注入攻击。
- 设置 innerHTML 将会破坏现有的已注册了事件处理函数的 HTML 元素，会在某些浏览器上引起内存泄露的潜在危险。 
- 你不能得到刚刚创建的元素的引用，需要你手动添加代码才能取得那些引用（使用 DOM APIs）。
- 你不能在所有浏览器的所有 HTML 元素上设置 innerHTML 属性（比如，Internet Explorer 不允许你在表格的行元素上设置innerHTML 属性）。 

> http://www.julienlecomte.net/blog/2007/12/38/   
> http://www.blueidea.com/tech/web/2008/5888.asp

innerHTML里插入`<script>alert('I am John in an annoying alert!')</script>`等脚本代码，会引发安全问题。但是html5标准已经指出：通过innerHTML插入的script不应该被执行。

对于一些非可显示字符，例如\n、\f、\t（通过textarea或iframe可插入）等，写入innerHTML属性时会被过滤掉（IE8及以下测试），但在textContent属性下，会原封不动的显示出来，现代浏览器允许读取element.textContent属性，但IE8以及老版本仍旧不允许读取，此处给编辑器相关项目带来一定困难。

## 总结
innerHTML方法虽然高效便捷，但**不可滥用**。使用时首先需要注意其可能引起的一些问题，其次在项目中需要权衡：比如想要的到新创建元素的引用，用innerHTML还需要通过其他元素查找到此元素再引用之；如果用dom menthods,就不必如此麻烦。

## 引用：
- http://www.julienlecomte.net/blog/2007/12/38/  
- http://slayeroffice.com/articles/innerHTML_alternatives/
- https://developer.mozilla.org/en-US/docs/DOM/element.innerHTML
- http://www.quirksmode.org/dom/innerhtml.html
- http://helephant.com/2007/03/15/interesting-side-affect-of-creating-dom-objects-using-innerhtml/
