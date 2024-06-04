---
layout: post
title: "angular.js系列七：项目开发建议"
tags: [js]
author_name: warmhug
---

### 目录结构：

1. 主要按 “组件类型” 分：

``` 
	├── app
	│   ├── app.js
	│   ├── controllers
	│   │   ├── page1
	│   │   │   ├── FirstCtrl.js
	│   │   └── page2
	│   │       └── ThirdCtrl.js
	│   ├── directives
	│   │   ├── page1
	│   │   │   └── directive1.js
	│   │   └── page2
	│   │       ├── directive2.js
	│   ├── filters
	│   │   ├── page1
	│   └── services
	│       ├── CommonService.js
	├── lib
	└── test
```	

2. 主要按 “业务模块” 分：

```	
	├── app
	│   ├── app.js
	│   ├── common
	│   │   ├── controllers
	│   │   ├── directives
	│   │   ├── filters
	│   │   └── services
	│   ├── page1
	│   │   ├── controllers
	│   │   │   ├── FirstCtrl.js
	│   │   ├── directives
	│   │   │   └── directive1.js
	│   │   ├── filters
	│   │   │   ├── filter1.js
	│   │   └── services
	│   │       ├── service1.js
	│   └── page2
	│       ├── controllers
	│       │   └── ThirdCtrl.js
	│       ├── directives
	│       │   ├── directive2.js
	│       ├── filters
	│       │   └── filter3.js
	│       └── services
	│           └── service3.js
	├── lib
	└── test
```

这两种划分方法都可以，个人觉得如果业务简单，按 组件类型 划分好点；业务复杂，按 业务模块 划分会更好点。

### 性能优化 ###

由于digest loop越多，性能问题越大，所以：

- Watch only the most vital variables
- Make computations in $watch as simple as possible. Making heavy and slow computations in a single $watch will slow down the whole application
- Make your filters as light as possible. They are called often during the $digest loop so creating a slow filter will slow down your app.

其他优化点：

- For session-level cache you can use `$cacheFactory`. This should be used to cache results from requests or heavy computations.
- Use `ng-bind` or `ng-cloak` instead of simple {% raw %}`{{ }}`{% endraw %} to prevent flashing content.
- Avoid writing complex code in the template.
- Controller and directives emit an event right before they are destroyed. 注册`$scope.$on('$destroy', ...)`来销毁 插件或事件监听

### 代码书写与命名 ###

- 使用 `$timeout`、`$window`、`$document`、`$http` 代替 `setTimeout`、`window`、`document`、`$.ajax`，使得测试更容易
- 使用promises (`$q`) 代替 callbacks，使得代码看起来更优雅
- 使用controllers 代替 `ngInit`
- directive之间能直接通过directive-controllers来通信
- Use `resolve` to resolve dependencies before the view is shown.
- 命名 directive、filters 使用 lowerCamelCase 方式
- 开发reusable components时创建一个isolated scope
- 注意`ng-style`怎么合适使用

controller相关：

- 不要在controllers中操作dom，使用directive代替
- 命名以`Ctrl`结尾，首字母大写。例如：`HomePageCtrl`
- 不要定义为全局变量，Use array syntax for controller definitions
- 确保controller内容尽可能精简，把常用函数抽象进一个service里
- 不同controller通信用 '函数调用' 方式 (子向父通信) 或者用$emit, $broadcast, $on等方法
- 格式化数据使用filter

更多参考 [angularjs-style-guide](https://github.com/mgechev/angularjs-style-guide)、[wiki](https://github.com/angular/angular.js/wiki/_pages)


### 引用 ###
- [http://www.cnblogs.com/whitewolf/archive/2013/03/24/2979344.html](http://www.cnblogs.com/whitewolf/archive/2013/03/24/2979344.html)
- [https://github.com/angular/angular.js/wiki/Best-Practices](https://github.com/angular/angular.js/wiki/Best-Practices)