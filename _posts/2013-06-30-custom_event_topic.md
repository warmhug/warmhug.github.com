---
layout: post
title: "自定义事件 & 延伸问题探讨"
tags: [js]
author_name: warmhug
---

自定义事件的常见定义：

- 事件vs方法：事件是指附着在dom上的一些方法；方法不限于dom，也包括js对象上的方法
- dom事件：一是产生浏览器行为的原生事件(eg.click input..)；二是自己命名的事件
- 自定义事件：即非浏览器原生的自己命名的事件

这样以来，我们明确要探讨的是"dom上的自定义事件"。

**1、自定义事件的注册**  
注册原生事件仅仅需要如下三行代码中的一行： 

```js
dom.onclick = handler;  
dom.addEventListener('click', handler, capture);  
dom.attachEvent('click', handler);  
```

自定义事件的注册也同样需要上述代码，但还需要以下代码支持：  

```js
//document.createEvent()创建自定义事件
var custom = document.createEvent("HTMLEvents");
custom.initEvent("customEvent", true, true);
custom.data = "Some data...";
```

很多库都是用的document.createEvent()方法，大概由于浏览器支持度比较好，但是标准已经建议弃用此方法，而推荐使用new CustomEvent()方法创建自定义事件。

**2、自定义事件的触发**  
原生事件的触发，一般是依赖于用户的操作，例如click、keydown事件，点击鼠标，按下键盘即会自动触发，这个过程浏览器本身已经帮忙处理好了。  
原生事件的触发，还有一种用程序触发的方式，即大家熟知的很多库或框架提供的trigger或fire方法。自定义事件的触发，就是用（大概也只能用）这种方法。这个方法是对浏览器原生触发事件方法dispatchEvent()或attachEvent() 的封装。

**3、自定义事件的删除**  
同原生事件一样：  

```js
removeEventListener()  
detachEvent()
```

**4、自定义事件的作用**  
由于自定义事件的触发，需要程序去执行，因此在触发事件之前我们便有机会做一些“预处理”操作；如果把自定义事件当做接口暴露给用户程序时，还能封装一些私有操作。举个例子：  
现有一个表单组件，提供$submit接口，而表单提交前一般需要验证数据的有效性，但普遍的验证方法（例如，必选、输入为数字等）完全可以让组件自身去实现，也即组件在submit方法里先把数据进行验证，然后执行抛出去的$submit接口事件。  
js设计模式中的“观察者模式”，也即利用此种方法来实现。很显然，这样子做的话，业务代码之间的耦合也减少了许多。

### dom原生方法重写 ###
例如我们要写个封装了浏览器差异的添加事件方法addEvent，有两种方式：  
1、在dom原型链上扩展  
HTMLElement.prototype.addEvent = function(){} // ie8以上的现代浏览器中  
遍历所有DOM，每个元素都直接添加addEvent方法  // ie6、7  
这是prototype.js和MooTools等库的做法。  
2、在dom上包装一层，做扩展  
例如jquery，把addEvent方法附在js的 $ 对象上。

jquery这种并不扩展或覆盖dom原形链上的方法，而是自己包裹一层再做处理的做法，可谓是“好好人”的风格吧。如果我们统一而又严重的依赖着jquery，也即我们很少需要用dom原生方法操作dom，而几乎都是$('#xx')等jquery包裹对象在处理着各种事物，那么jquery对象也即是更高层次的dom对象而已，就像形成了自给自足的闭环空间，这样就没什么可挑剔的。 
 
但事实上，有些应用除了需要jquery，还需要其他的jquery以及其插件不能提供的东西做支持。就像我们要想为页面中所有元素绑定的所有事件提供一个统一的销毁事件方法teardown，调用一下page.teardown()那么整个页面中的所有事件全部销毁。要实现这个需要做什么？首先要在事件注册时做某某元素注册了某某事件的记录信息，这就要改写addEvent方法；另外就是事件销毁时，要分级别（例如组件实例级、组件类级、整个页面级）的做销毁，就要遍历已经记录的要销毁的元素，委托到统一的销毁方法teardown上做处理。这个例子发源自于twitter的[flight.js](https://github.com/twitter/flight)。

扩展或覆盖dom原型链的方法相信不少人会不太同意，抛开老版本ie的兼容性不谈，它还是会引起不少潜在问题：有可能会与未来浏览器提供的方法重名；每个元素都能用这个扩展方法；不同库之间的冲突与不兼容等。。。也就是说这样做了，代码维护成本必将提升，别人使用起来也得小心翼翼。  
但这么做的好处也自然存在，当覆盖dom原生方法时，由于在最底层的api上做了操作，其他库或业务代码最终都是需要调用此api的，这样就控制了所有调用，干自己想干的额外事情，给这些调用者提供额外功能，同时又不需要它们修改代码而能直接享用额外功能的好处。如果结合上段中的对页面中事件进行管理，用覆盖dom原生的addEventListener的方法，做一个事件管理器，那么别人使用这个管理器，也就不需要再修改已经写好的代码了。

自定义事件的作用也不止于此，另外扩展或覆盖原生方法到底是好是坏呢？

## refer 
- [http://www.zhangxinxu.com/wordpress/2012/04/js-dom%E8%87%AA%E5%AE%9A%E4%B9%89%E4%BA%8B%E4%BB%B6/](http://www.zhangxinxu.com/wordpress/2012/04/js-dom%E8%87%AA%E5%AE%9A%E4%B9%89%E4%BA%8B%E4%BB%B6/)
- [http://www.sitepoint.com/javascript-custom-events/](http://www.sitepoint.com/javascript-custom-events/)