---
layout: post
title: "angular.js系列四：transclude与Shadow DOM、promise应用"
tags: [js]
author_name: warmhug
---

### 一： transclude 与 Shadow DOM 
这里为什么独立讲讲transclude？它不是个标准的英文单词，从这个[ngTransclude](http://docs.angularjs.org/api/ng.directive:ngTransclude) api里看它也不复杂，而且似乎也没什么与众不同的地方。是的，其实不必特意讲，只因为是它跟shadow dom有类似的地方，这个似乎证明angular的确是一个面向未来浏览器的框架（AngularJS is banking on the future of JS and trying to improve and extend HTML and JS by talking directly with the standards.  In the future, most of the AngularJS concepts will be part of the browser but you can already use them with AngularJS if like me you can't wait.）。

从[ngTransclude](http://docs.angularjs.org/api/ng.directive:ngTransclude)下边的Example可以理解transclude的用法和作用，这里摘录出来关键的一段代码作解释：

```js
angular.module('transclude', [])
 .directive('pane', function(){
	return {
	  restrict: 'E',
	  transclude: true,
	  scope: { title:'@' },
	  template: '<div style="border: 1px solid black;">' +
				  '<div style="background-color: gray">{{title}}</div>' +
				  '<div ng-transclude></div>' +
				'</div>'
	};
});
```

在这个pane组件的Directive Definition Object上`transclude: true`规定了把`<pane>`自定义元素内的html添加进`<div ng-transclude></div>`这个标签内。

Shadow DOM是怎么做的？如下（需要设置：chrome://flags -- 启用实验性JavaScript （Enable Experimental JavaScript）；开发者工具，勾选Show Shadow DOM）：  

```html
<div id="host">
    <p><span>this is light dom</span></p>
</div>
<script>
    var root = document.querySelector('#host').webkitCreateShadowRoot();
    root.innerHTML =
        '<div style="border: 1px solid black;">' +
            '<div style="background-color: gray">this is shadow dom : title</div>' +
            '<div id="content">' +
                '<content></content>' +
            '</div>' +
        'hello shadow dom</div>';
</script>
```

看起来就这么简单！不过shadow dom还有更细节的内容，像如何应用样式，与新的template元素标记结合等，感兴趣的自行查找学习。详细的参考[WD-components-intro](http://www.w3.org/TR/2012/WD-components-intro-20120522/#shadow-dom-section)。


#### 引申： 

有关angular的哪些特性，是未来浏览器将会支持的，[这里](http://www.quora.com/Client-side-MVC/Is-Angular-js-or-Ember-js-the-better-choice-for-Javascript-frameworks)的评论中来自Patrick Aljord 的两段话阐明了这些：  
1): **[MDV](https://github.com/toolkitchen/mdv/blob/master/README.md)**  
Here's how AngularJS does it:

	<p>{% raw %}{{hello}}{% endraw %}</p>

It makes writing dynamic code with HTML declarative again. And if you have to loop through an array, you just do:

```html
<ul>
  <li ng-repeat="element in array">element</li>
</ul>
```

This syntax looks exactly like the new MDV standard that you can check here [mdv](http://mdv.googlecode.com/git/docs/design_intro.html). This looks much cleaner than using Ember's mustache and again, when you learn AngularJS you actually learn the future of JS.

2): **[reusable components](http://www.w3.org/TR/2012/WD-components-intro-20120522/#custom-element-section)**  
> Another great concept of AngularJS are directives to have reusable components. This allows you to write very clean code and reuse your widgets easily. This is not AngularJS specific, this is the future of the web and it's a standard being implemented through web components Introduction to Web Components, even mozilla is working on it and have released a shim a cross-browser web components library.


### 二： asynchronous requests and promise 

要展示应用程序，必须先要通过ajax请求来数据，angular内置了$http服务和更高层的封装$resource服务，其使用方式也类似于jquery中的ajax，如果是简单的需求，依照api提供的方法，直接获取数据展示即可，这里讨论些稍微复杂的情形，这便还需要引出promise。

关于promise，网上资料已经很多，也有很多相应的封装库。

[这里](https://github.com/warmhug/warmhug.github.com/blob/ecbaf3fee79e9f0f0bb38b3a9604f8ac8f58c2d1/demo/angular-sty/series_4/http-promise.html)的demo用$timeout、$q、$http做了个简单的请求数据示例，其中就能很好体现promise的优势。

另外还要结合[$route](http://code.angularjs.org/1.0.7/docs/api/ng.$route)描述一些常用示例。当app的router切换时，一般都会涉及到数据的请求，这便有以下情形：

1. 边请求数据，边初始化页面
2. 数据请求完毕，再初始化页面
3. 多个数据源全部请求完毕（依序或无序），才能初始化页面

第一种情形，按照我们上边的简单demo示例做法就能搞定。第二三种情形，就涉及到route的配置和promise的应用了，分析如下。

[$routeProvider](http://docs.angularjs.org/api/ngRoute.$routeProvider)的api配置里有这个`resolve`，其描述是这样：
> An optional map of dependencies which should be injected into the controller. If any of these dependencies are promises, they will be resolved and converted to a value before the controller is instantiated and the $routeChangeSuccess event is fired.

大意是：如果依赖是promise，那么在controller实例化和$routeChangeSuccess事件触发前，需要等到promise的resolve执行并转换成value值。看一个实际的[demo](https://github.com/warmhug/warmhug.github.com/blob/ecbaf3fee79e9f0f0bb38b3a9604f8ac8f58c2d1/demo/angular-sty/series_4/route-promise-part1.html)，这里是关键的一段：

```js
resolve: {
  'MyServiceData': function (MyService) {
      return MyService.promise;
  }
}
```

试试把这段注释掉，再和之前对比下app.controller中的console.log值，看看有什么不一样？这样就会返回null了，这就说明了controller实例的执行，是先等待了数据请求成功。

上边说到的第三种情形：多个数据源，有序或无序请求，完成之后实例化constroller。这里还是提供demo说明之：[无序请求](https://github.com/warmhug/warmhug.github.com/blob/ecbaf3fee79e9f0f0bb38b3a9604f8ac8f58c2d1/demo/angular-sty/series_4/route-promise-part2.html)，[有序请求](https://github.com/warmhug/warmhug.github.com/blob/ecbaf3fee79e9f0f0bb38b3a9604f8ac8f58c2d1/demo/angular-sty/series_4/route-promise-part3.html) 看源码会发现，无序请求只需在route配置中的resolve上多配置个参数即可；但有序请求稍微复杂点：

```js
MyService.promise.then(function () {
    //do init stuff that depends on MyService (if any)
    console.log("Resolved: " + MyService.doStuff());
    return promise2;
});
```

需要在先发请求的promise获得数据（即resolve）后，再返回后续的promise（即发后续请求），route配置中的resolve上只需要最后一个promise即可。

最后再看一个实际应用级别的[demo](https://github.com/warmhug/warmhug.github.com/blob/ecbaf3fee79e9f0f0bb38b3a9604f8ac8f58c2d1/demo/angular-sty/series_4/route-promise-full.html)

#### 引用
- [http://stackoverflow.com/questions/16286605/initialize-angularjs-service-with-asynchronous-data](http://stackoverflow.com/questions/16286605/initialize-angularjs-service-with-asynchronous-data)
- [http://stackoverflow.com/questions/11972026/delaying-angularjs-route-change-until-model-loaded-to-prevent-flicker](http://stackoverflow.com/questions/11972026/delaying-angularjs-route-change-until-model-loaded-to-prevent-flicker)
- [http://www.javierlerones.com/2013/07/preloading-data-using-deferred-promises.html](http://www.javierlerones.com/2013/07/preloading-data-using-deferred-promises.html) 


