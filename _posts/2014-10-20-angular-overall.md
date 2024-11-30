---
layout: post
title: "angular(1.x) - 总复习"
tags: [js]
author_name: warmhug
---

> [why AngularJS](http://jeffwhelpley.com/angularjs/)  -- vs --  [The reason Angular JS will fail](https://news.ycombinator.com/item?id=7522520)
> [专访AngularJS框架创始人](http://www.csdn.net/article/2013-10-08/2817118-AngularJS-Framework-Google)

## 适用业务场景

I really like Angular, but I think it's best suited for single-page applications and dynamic forms (which is what I use it for). In that context, I've been very pleased with Angular and it's saved me a ton of time.

It is quite possible to sprinkle a little Angular on your round trip app without ever using Ajax.

[Mixing Spring MVC with Angular.JS：](http://stackoverflow.com/questions/25997566/spring-mvc-as-a-rest-provider-with-angularjs-vs-jsp-velocity-freemarker-for-the)

## 项目目录组织

[angular-seed](https://github.com/angular/angular-seed)、[angular-requirejs-seed](https://github.com/tnajdek/angular-requirejs-seed)
[不同规模app文件组织方式介绍](http://codingsmackdown.tv/blog/2013/04/19/angularjs-modules-for-great-justice/)、[示例](https://github.com/lavinjj/angularjs-modules-for-great-justice)
[ng-boilerplate](http://joshdmiller.github.io/ng-boilerplate/#/home)、[Scalable code organization in AngularJS](https://medium.com/opinionated-angularjs/scalable-code-organization-in-angularjs-9f01b594bf06)

[AngularJS CRUD application demo](https://github.com/angular-app/angular-app)，对于较大型应用，按不同功能区块划分、组织文件，达到业务组件化目的。如下：

    -- /app
       |-- /admin （管理后台）
       |  |-- /projects
       |  |  |-- projects.js  内容如：
                  angular.module('projects', [ 'xx' ])
                  .config(['xProvider', function(xProvider){ ... }])
                  .controller('projectsCtrl', ['$scope', function($scope){ ... }])
       |  |  |-- xxDirective.js
       |  |  |-- yyDirective.js  内容如：
                  angular.module('projects.xx')
                  .directive('uniqueEmail', ["Users", function (Users) { ... }])
       |  |  |-- projects.tpl.html
       |  |  |-- projects-list.tpl.html
       |  |-- /users
       |  |  |-- users.js
       |  |  |-- users.tpl.html
       |  |-- admin.js
       |-- /dashboard （仪表盘）
       |  |-- dashboard.js
       |  |-- dashboard.tpl.html
       |-- /other （其他）
       |-- /app.js
    -- /common      （全局通用的功能js）
       |-- /directives
       |-- |-- modal.js
       |-- |-- crud.js
       |-- /services
       |-- |-- i18n.js
       |-- |-- notifications.js
       |-- |-- authorization.js  内容如：
                app.factory('auth', ['$http', function($http){
                    return {
                      getAuth: function (arg) {
                        var promise = $http.get(arg)
                        return promise
                      }
                    }
                }])
       |-- /resources （REST应用中，全局资源抽象）
       |-- |-- tasks.js
       |-- |-- users.js  内容如：
                angular.module('resources.users').factory('Users', ['xxResource', function (  xxResource) {
                  var userResource = xxResource('users');
                  userResource.prototype.getFullName = function () {
                    return this.lastName + " " + this.firstName + " (" + this.email + ")";
                  };
                  return userResource;
                }]);
    -- /assets
       |-- /images
       |-- /less
       |-- /libs
       |-- /vendor
    -- /test
       测试目录组织与上述目录一致

当某个功能controller处理内容过多时，可抽离逻辑到services块中，以尽量使controller更薄

# 入门与进阶

经典入门教程：[官网首页的介绍及几个demo](https://angularjs.org/)，[官方tutorial](https://code.angularjs.org/1.2.16/docs/tutorial)与[中文翻译](http://angularjs.cn/T006)。 该教程利用git及bower等工具，分步骤有序的讲述各个点。 **通过此教程，能对angular有一个初步的、较全面的认识。**

> 重点：
> [angular与jQuery思维模式区别 - Think in AngularJS ](http://stackoverflow.com/questions/14994391/how-do-i-think-in-angularjs-if-i-have-a-jquery-background)、
> [中文翻译](http://damoqiongqiu.iteye.com/blog/1926475)

进阶学习资源有[官方开发者文档](https://code.angularjs.org/1.2.16/docs/guide)、[中文社区开发者文档](http://angularjs.cn/T008)、[AngularJS中文社区](http://angularjs.cn/)、[angular编码指南](https://github.com/mgechev/angularjs-style-guide)。一眼看去、内容也太多了点。可根据官方推荐学习路线依次进行：

- angular总括、Data binding、Expressions、Directives、Forms、Scopes、Controllers及相关的model view概念；创建services及对“依赖注入”。
- module、router、filter、$location、Animations、测试、本地化...等细节的学习。

逐步进阶学习，会发现一些比较重点、比较集中的点要掌握，如：

1. directive的各个配置项的含义及用法，$compile $parse的使用。
2. scope的几种类型，父子继承关系的[理解](https://github.com/angular/angular.js/wiki/Understanding-Scopes)，一些方法如 $watch $apply $eval $destroy的使用，用作消息通信的 $on $emit $broadcast 方法的使用。
3. 异步加载模板、route切换时 $q promise的使用；非angular组件如何融入进来。
4. form相关的ng-model、$render、$setValidity、$setViewValue、$parsers等使用。
5. 框架提供的各种概念的设计以及框架本身代码的理解。

#### 这里对scope进行些总结：

- scope是controller和view之间的‘粘合剂’。
    - controllers和directives都有引用scope，但彼此互不了解，也就是controllers和view之间互不了解，这样就提高了controller的可测性。
- scope上的属性是被用于渲染view的数据，并且是view上数据的唯一来源（single source-of-truth）。
- scope的结构：root scope → child scopes → child scopes → … 产生类似于dom的树结构，子scope属性能继承父scope属性（isolate scope需要额外声明才可继承）。
- 获取某个元素的scope，可通过 angular.element(ele).scope() 方法。
- scope 能像 DOM events一样触发事件，事件能够向下广播($broadcast)到子scope上，或者向上发射($emit)到父scope上。
- scope的生命周期：创建（通过$injector）– Watcher registration – Model mutation（angular之外的环境导致的model改变，需要用$apply()） – Mutation observation – Scope destruction（不使用时销毁掉）


### 一些最佳实践:

- 很多AngularJS新手把指令当成容纳各种jQuery代码的场所，简单的把DOM操作相关的代码放到指令里面，这是错误的。。 要思考用“AngularJS的方式”来实现它，当你在应用里面的任何地方进行DOM操作之前，请问问自己是不是真的必须要这样做。
- directive里的link函数内，也应该多放业务逻辑代码，而非模板代码、模板操作，例如class变换、click事件注册等，要用 ng-class  ng-click 等指令放到模板中，而不是在link函数里 addClass 、on(‘click’)，这样便于切换模板，便于单元测试。（但这样也会加剧性能问题）
- 实际上directive指令是HTML扩展。如果HTML无法做到你想实现的某件事情，你就自己编写一个指令，然后再去使用这个指令，好像它就是HTML的一部分一样。
- 什么时候该用 directive、controller、services ?
    - When do we use services? Whenever we want to share data across domains.
    - Controllers should be used purely to wire up services, dependencies and other objects, and assign them to the view via scope. They’re also an excellent choice when needing to handle complex business logic in your views.
    - the majority of the application’s complexity is in the directives. They are a powerful tool for working with and modifying the DOM

-----

# 高级

## 综合
- [angularjs-in-patterns](https://github.com/mgechev/angularjs-in-patterns)
- [angular资源大集合](http://www.planningforaliens.com/angular/ginormous-unstoppable-angular-resource-list/)
，angular各种细节写法建议：[1](http://toddmotto.com/opinionated-angular-js-styleguide-for-teams/)
、[2](http://www.codelord.net/2014/05/26/angularjs-decisions-decisions-and-my-choices/)
、[controller as](http://toddmotto.com/digging-into-angulars-controller-as-syntax/)
- [angularJS的Java起源：Angular vs JSF vs GWT](http://blog.jhades.org/the-java-origins-of-angular-js-angular-vs-jsf-vs-gwt/)
- [比较全面讨论模块化开发：(Require.js、Browserify、Angular DI、ES6 modules )](https://medium.com/@dickeyxxx/best-practices-for-building-angular-js-apps-266c1a4a6917)

## scope专题
- 常用api：[$parse vs $eval](http://stackoverflow.com/questions/15671471/angular-js-how-does-eval-work-and-why-is-it-different-from-vanilla-eval)、[深入$parse](https://umur.io/advanced-angular-parse/)

- [理解$watch ，$apply 和 $digest --- 理解数据绑定过程](http://www.angularjs.cn/A0a6)、[原文](http://angular-tips.com/blog/2013/08/watch-how-the-apply-runs-a-digest/)

- [$watch $digest $apply介绍，及使用时机建议：](http://www.benlesh.com/2013/08/angularjs-watch-digest-and-apply-oh-my.html)
    - use $watch in directives ； DON'T use $watch in a controller.
    - use $digest/$apply in directives and services ； DON'T use $digest/$apply in a controller.

- [scope.$apply ，何时使用$apply？](http://jimhoskins.com/2012/12/17/angularjs-and-apply.html)
    - If you write any code that uses Ajax without $http, or listens for events without using Angular’s ng-* listeners, or sets a timeout without $timeout, you should wrap your code in $scope.$apply

- [$apply与$digest的使用区别：](http://stackoverflow.com/questions/18697745/apply-vs-digest-in-directive-testing) 使用digest性能会更好
    - scope.$digest() will only fire watchers on current scope, scope.$apply will evaluate passed function and run $rootScope.$digest().

- [$digest loop生命周期详解--$evalAsync与$timeout使用区别](http://ruoyusun.com/2013/08/24/a-glimpse-of-angularjs-scope-via-example.html)

- [深入scope - 各个地方的scope介绍](http://modernweb.com/2014/07/14/leverage-scope-creep-depth-tutorial-angular-js-scope/)

- [封闭scope详解 (注意对&的解释)](http://www.codetunnel.io/isolate-scopes-explained/)

## directive专题

- [理解directive](https://github.com/angular/angular.js/wiki/Understanding-Directives)
- [directive-to-directive-communication](https://thinkster.io/egghead/directive-to-directive-communication/)

- directive scope 中 `@`、`=`、`&` 的区别：[1](http://stackoverflow.com/questions/14050195/what-is-the-difference-between-and-in-directive-scope)、[2](http://stackoverflow.com/questions/14908133/what-is-the-difference-between-vs-and-in-angularjs/)、 [isolate-scope-and-function-parameters](https://weblogs.asp.net/dwahlin/creating-custom-angularjs-directives-part-3-isolate-scope-and-function-parameters/)

- Compile, Controller, Pre-link, Post-Link 的执行顺序及分别做了什么？ [参考](http://odetocode.com/blogs/scott/archive/2014/05/28/compile-pre-and-post-linking-in-angularjs.aspx) 、[深入理解 compile, pre-link and post-link function 的不同](http://www.jvandemo.com/the-nitty-gritty-of-compile-and-link-functions-inside-angularjs-directives/)

- transclude: 、[介绍](http://www.cnblogs.com/cunjieliu/p/4055519.html)、[angular中的Transclude](http://blog.omkarpatil.com/2012/11/transclude-in-angularjs.html) 、[angular1.2中的Transclude()的变化](http://www.bennadel.com/blog/2561-changes-in-transclude-function-availability-in-angularjs-1-2.htm)
、[transclude:'element' 使用示例](http://jsfiddle.net/codef0rmer/6yZKQ/)、[transclusion-and-scopes](http://angular-tips.com/blog/2014/03/transclusion-and-scopes/)

- $observe() 与 $watch区别：
    - $observe() is a method on the Attributes object, and as such, it can only be used to observe/watch the value change of a DOM attribute. It is only used/called inside directives. Use $observe when you need to observe/watch a DOM attribute that contains interpolation . E. then in a directive: attrs.$observe('attr1', ...). (If you try scope.$watch(attrs.attr1, ...) it won't work because of the -- you'll get undefined.) Use $watch for everything else.

- 兄弟directive通信(sibling directive communicate):

#### link细节：directive所在元素的子元素何时dom ready？

link函数在template被克隆后调用，不包括那些发生在模板克隆动作之后的，通过ng-repeat或ng-view生成的dom元素，即directive所在element的的子元素(含类似ng-repeat) 此时还没被渲染好。如果想在这些子元素都渲染好后，再执行操作(例如jQuery操作这些dom)，那就需要在link函数里设置延时，可使用 $timeout，见[示例](https://github.com/warmhug/warmhug.github.com/blob/ecbaf3fee79e9f0f0bb38b3a9604f8ac8f58c2d1/demo/angular-sty/dom-ready.html)。

更进一步，如果你想获取这些子元素最新的 position, offset, height, width，上边获取到的可能还不准确。可能还需要再嵌套一个 $timeout，这样就给浏览器更多渲染时间，不然浏览器可能没有足够的时间去完全渲染布局好。示例如下：

```js
    link: function postLink(elem, attrs, transclude) {
        $timeout(function () {
            $timeout(function () {
                // This code will run after templateUrl has been loaded, cloned
                // and transformed by directives. and properly rendered by the browser
            }, 0);
        }, 0);
    }
```

### 一些最佳实践：

- directives里的 controller 和 link 该分别何时使用？
    - controller can expose an API, and link functions can interact with controllers using require.
    - use controller when you want to expose an API to other directives. Otherwise use link.

- 不要在ng-repeat元素上应用其他directive：
    - the transformation ng-repeat applies to the DOM is substantial. applying other directives (such as ng-show, ng-controller and others) to the same element as ng-repeat generally leads to problems


-------

## DI相关：
- injector相关：injector.js源码分析、[Can't retrieve the injector](http://stackoverflow.com/questions/13400687/cant-retrieve-the-injector-from-angular)、[$injector.instantiate()使用demo](http://jsfiddle.net/alimills/Xn4Z2/)
- [DI annotation 详解](http://toddmotto.com/angular-js-dependency-injection-annotation-process/)


## angular的module：

- module覆盖问题：
    - You should note that if you do create another module with a module name that has already been created earlier, then the old module will be overwritten – and all the registered factories etc on that module will be lost. [详细链接](http://blog.nebithi.com/angularjs-clearing-a-few-confusion-points/)

- module上`service、factory、provider`详解：[全面了解angular provider](http://www.garabagne.io/2014/06/08/the-life-and-times-of-the-angular-provider/)、[differences-between-providers-in-angularjs](http://blog.xebia.com/2013/09/01/differences-between-providers-in-angularjs/)、[原理及入门](http://www.mikeobrien.net/blog/angular-consts-values-services-factories-and-providers-oh-my/)、[区别、注意decorator](http://angular-tips.com/blog/2013/08/understanding-service-types/)


### module及其附加的方法上命名冲突问题：
问题描述：As of today AngularJS doesn't handle namespace collisions for services so if you've got 2 different modules with the service named the same way and you include both modules in your app, only one service will be available.

目前的解决办法：For the moment the best option is to prefix service names with a custom prefix, ex:

```js
angular.module('myprefix_mymodule',['dep1', 'dep2']).factory('myprefix_MyService', ...)
```

总结下：**目前最好是 通过约定命名空间 前/后缀**来解决，但很显然 这不是最好最彻底的办法，有许多人期待angular官方能从根本上解决此问题。

> [详细链接](http://stackoverflow.com/questions/14909474/namespacing-services-in-angularjs)、[现在为什么存在此问题](http://stackoverflow.com/questions/13406791/modules-and-name-clashes-in-angularjs/19820088#19820088)、[此问题的issue讨论](https://github.com/angular/angular.js/issues/2767)、 通过angular.injector方式的并不太好的[解决方法](http://stackoverflow.com/questions/21594441/angularjs-injecting-a-factory-service-with-identical-name-but-under-different)




## 启动问题(ng-app自动启动 vs angular.bootstrap()手动启动 )
页面中有多个`ng-app`时，只有第一个会起作用；当ng-app="app"、然后通过 `angular.module('app', [])` 对页面 **启动** 后就 **不能再次这么写：`angular.module('app', [])`** ，否则就会报错(考虑这种情况：非单页应用、不同系统里的html片段都有这段代码，这些片段再一起组合成新页面，就会出错)。解决办法如下：

这里是[以下示例的文件地址](https://github.com/warmhug/warmhug.github.com/tree/ecbaf3fee79e9f0f0bb38b3a9604f8ac8f58c2d1/demo/angular-sty/bootstrap-app)

- 方法一：仍然利用`ng-app`，遵循官方约束、确保只启动一次，示例见 `./ng-app.html`。
    - 方法1.1，对`angular.module()`进行hack修补、这样可以多次写`angular.module('app', ['xx'])`而不会报错，看着像能多次启动(其实不是)，示例见 `./ng-app1.html`。

- 方法二：不用`ng-app`，改用`angular.bootstrap()`方法进行局部“手动启动”。示例见`./manual-bootstrap.html`、`./manual-bootstrap1.html`，对比可发现：对于“并列的互不嵌套的dom片段”，分别手动启动不会有问题；但对于“有嵌套的dom结构”，手动启动会有冲突的问题。。。
    - 方法2.1，手动启动可能需要多次写`angular.bootstrap()`、显得比较繁琐，一种办法是把它们包装到一个新的指令中来做简化，介绍详见[此处](http://www.simplygoodcode.com/2014/04/angularjs-getting-around-ngapp-limitations-with-ngmodule/)；示例见 `./ng-module.html`





## $sce (Strict Contextual Escaping)

angular1.2之后，去掉了`ng-bind-html-unsafe` 这个directive(angular1.0.x中有)，但引入了 [$sce](https://code.angularjs.org/1.2.16/docs/api/ng/service/$sce) 来对插入的HTML进行安全控制。对于这样的`<p style="color:red;" onclick="alert(1)">段落</p>`带style onclick的html，只用进行`$sce.trustAsHtml(html)`(可封装成filter)做信任处理，然后便可用`ng-bind-html`把它原样插入dom中；否则会把style、onclick过滤掉，因为angular认为他们是不安全的。

更便捷地我们可以对信任的域(象html所在的域、资源文件域)加上白名单，做法是在config阶段用`$sceDelegateProvider.resourceUrlWhitelist()`添加信任列表。

更彻底的做法是，禁止掉`$sce`，代码示例：

```js
    app.config(function ($sceProvider) {
        $sceProvider.enabled(false);  // 完全禁掉$sce
    });
```

**注意另一个情况：** html里含有 directive 的情况，如：`<button ng-click="alert()"></button>`，把它用ng-bind-html插入到dom后，点击这个button，并不会触发ng-click注册的事件，因为这些directive需要先被编译。做法见[此链接](https://github.com/angular/angular.js/issues/4992)以及[compile-directive](https://github.com/warmhug/warmhug.github.com/blob/ecbaf3fee79e9f0f0bb38b3a9604f8ac8f58c2d1/demo/angular-sty/compile-directive.html)。即：ng-bind-html不会调用$compile服务，所以不会处理ng-click这些directive，你需要自己写directive去处理这种情况。




## `$httpProvider.interceptors`可能引起的问题

当在所有ajax请求（get / post）中加 token 等统一的`params`时，会影响 directive 中的 templateUrl 的正确获取。因为 templateUrl 会经过`$http`服务混合$httpProvider.interceptors 插入的参数、先构造出新的url（见angular-1.2.16 中 8145行 buildUrl函数），导致模板地址与原本指定的不同，不能利用本地`script template`，而是直接发起请求远程模板、但远程不存在此模板。
![a](https://tfsimg.alipay.com/images/T1YWxcXjhyXXXXXXXX.png)


------



# 业务应用

## SPA
单页应用注意点：模板：统一规划与处理；route：浏览器历史记录、转场效果；route模板的远程拉取（跨域）

- [ui-router](https://github.com/angular-ui/ui-router)
- [AngularJS service to handle Rest API Restful Resources](https://github.com/mgonto/restangular)
- [$routeProvider resolve](http://stackoverflow.com/questions/16286605/initialize-angularjs-service-with-asynchronous-data)

### Authentication
- [authentication-made-simple-in-single-page-angularjs-applications](http://brewhouse.io/blog/2014/12/09/authentication-made-simple-in-single-page-angularjs-applications.html)
- [satellizer](https://github.com/sahat/satellizer)：Token-based AngularJS Authentication
- [token-based-authentication](http://code.tutsplus.com/tutorials/token-based-authentication-with-angularjs-nodejs--cms-22543)


## 测试：
- [angular的各部分测试](https://www.airpair.com/angularjs/posts/testing-angular-with-karma)、[1](http://quickleft.com/blog/angularjs-unit-testing-for-real-though)、[2](http://www.cnblogs.com/whitewolf/p/3807063.html)


## 库、组件
[angular-ui](http://angular-ui.github.io/)、[angular-strap(ie8支持不好)](https://github.com/mgcrea/angular-strap)

### form相关：
- [AngularJS 1.3 - Forms Example ](http://yearofmoo-angularjs-forms.herokuapp.com/#/)
- [ngMessages is a new feature in AngularJS 1.3 for rending error messages in forms ](http://www.yearofmoo.com/2014/05/how-to-use-ngmessages-in-angularjs.html)、[ngMessages demo](https://yearofmoo.github.io/ngMessages/)

- [自定义验证库](http://huei90.github.io/angular-validation/)、[表单验证](http://johannesjo.github.io/ng-fab-form/)
- [用json表示并生成form](https://github.com/angular-formly/angular-formly)
- [数据编辑器](http://toodledo.github.io/ADE/)


### 各种：
- [全面的表格](http://bazalt-cms.com/ng-table/example/1)
、[简单的表格](http://lorenzofox3.github.io/smart-table-website/)
- [无尽列表](http://sroze.github.io/ngInfiniteScroll/)
- [angular-storage](https://github.com/auth0/angular-storage)
- [angular-loading-bar](http://chieffancypants.github.io/angular-loading-bar/)
- [类似微博@提醒功能](http://jeff-collins.github.io/ment.io/#/examples)
- [angular-data](http://angular-data.pseudobry.com/)
- [图表chart](http://blog.lingohub.com/developers/2014/06/comparison-angularjs-directives-charts-front-end-app-development/)、 n3-charts
- [调试技巧与工具](http://ionicframework.com/blog/angularjs-console/)
- ng-annotate、[firebase](https://www.firebase.com/how-it-works.html)、
[angular-classy](http://davej.github.io/angular-classy/)
- [angular-hint](http://blog.thoughtram.io/angularjs/2014/11/06/exploring-angular-1.3-angular-hint.html)
- [展示详细的log信息](http://nodejs.jsnlog.com/Documentation/GetStartedLogging/AngularJsErrorHandling)
- [angular-kendo](http://kendo-labs.github.io/angular-kendo/#/events)、[kendo-ui怎么转为angular-directive](http://modernweb.com/2014/02/03/how-kendo-ui-uses-kendo-ui-to-build-angular-directives-for-kendo-ui/)


## lazy load 与 loader：
angular原生不支持模块的lazy load，所以与require.js等loader结合使用时，会感到特别别扭，代码写法也要相应改变。具体示例如：[angular-requirejs-seed](https://github.com/tnajdek/angular-requirejs-seed)、[angularjs-lazy-loading-with-requirejs](https://github.com/ifyio/angularjs-lazy-loading-with-requirejs)

相关资料参考：[1](http://www.slideshare.net/nirkaufman/angularjs-lazy-loading-techniques)
、[2](http://ify.io/lazy-loading-in-angularjs/)
、[3](http://stackoverflow.com/questions/15250644/angularjs-loading-a-controller-dynamically)
、[4](http://www.startersquad.com/blog/angularjs-requirejs/)
、[5](http://kielczewski.eu/2013/04/integrating-angularjs-with-requirejs/)
、[6](http://thaiat.github.io/blog/2014/02/26/angularjs-and-requirejs-for-very-large-applications/)
