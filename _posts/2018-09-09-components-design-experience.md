---
layout: post
title: "前端组件设计杂谈"
tags: [js]
author_name: warmhug
---

前端发展到今天，组件化的概念已经深入人心，特别是 react 得到广泛应用以来，应该没有哪一个从业者会说自己从没写过 component 了！而自己这几年的工作、除了业务支持外主要的技术积淀、也都围绕着 ant design (mobile) 前端组件库。个人早期参与的是 PC 组件的建设、之前也专门针对 Tree / TreeSelect 组件总结了一些[开发心得](http://warmhug.github.io/2016/07/23/react-components-tree-tree-select.html)，近两三年主要参与建立起了 [mobile](https://github.com/ant-design/ant-design-mobile) 组件库。

PC 和 Mobile 组件开发大多地方都是类似的，主要的区别体现在前者是本身功能复杂、后者是运行环境复杂。本文涵盖了 PC 和 Mobile 组件，主要分析一些实际的 case 以及相应的设计方式，又因为主要是杂谈、所以不是很有“起承转合”的严谨工整文采风格~ 那么我们就开始聊聊吧！😁

### 还记得“早期经典”的组件设计吗？

1. 创建一个组件类

```js
function TreeView() {
}
```

2. 设置组件类的配置项或属性

```js
function TreeView(config) {
  this.cfg = extend({}, config);
}
```

> 另外：组件的属性 只能通过方法来访问；组件的属性 value change 后组件自动映射变化。

3. 组件类上附加组件方法

```js
TreeView.prototype.xx = function () { };
```

4. 为组件添加自定义事件

mixin 进来带有 on / off / fire 等方法的事件系统，为组件级别添加事件，屏蔽底层的 dom 事件，方便使用。

5. 抽出 Widget 抽象类，作用是为 ui 组件提供统一的接口名，统一生命周期管理。

最后、组件使用方式：new 出组件实例即可。

### 现在的组件设计

现在使用的 react / vue 等框架，我们组件写法和用法已经跟早期的有很大不同，比如我们很少写 new 了，而是都基于统一的框架 API 在写了。

```js
class TreeView extends React.Component {
  state = {};
  static getDerivedStateFromProps(props, prevState) {}
  render() {}
}
```

框架提供了 render / didmount 等生命周期函数，你只需在里边写需要的逻辑即可，其他活框架帮你干。

当然在 React 确定王者地位之前，比如 Backbone 库的设计 (View Events EventBus)、Angular 等相关众多的 MVVM 框架设计都成一时经典、有些至今应用依旧广泛。所以，组件设计甚至整个系统架构设计，可以说是 兵无定式 水无常形？😏

### 组件的设计原则

[react-component](https://github.com/react-component) 这里的大多数组件是 ant design 的底层依赖，他们大都很好的遵循了我们的一些设计原则，这里简单概括下：

- 职责清晰、单一职责
    - 组件里的每个模块，分别该承担某一个功能
    - 多个组件 / 模块协同完成一件事，而不是一个组件替其他组件完成本该它自己完成的事情

- 开放与封闭
    - 属性配置等 API 对外开放；组件内部 dom 及状态的更改、对外封闭

- 高内聚、低耦合
    - 组件内部通过 callback 方式直接调用，组件与组件之间通过发布订阅的模式通信

- 避免信息冗余
    - 例如：一个东西能被另一个推导出来，就只使用一个

- API 尽量和已知概念保持一致
    - API 命名：比如 聚焦 常用命名是 focusable 而不是 canFocus 等自己臆想的名字、还有如 onDeselect 等规范名字。
    - API 的功能要单一并表意：比如 active 表示活动状态、但不能代替表示 selected 选中状态。

这些原则有没有跟 OOP 或一些优秀软件架构的原则很类似？是的，像是真理的普适性~ 😀

### 组件的功能细节

Form 组件应该是涉及到数据类的前端应用最需要的组件之一，我们以 antd 依赖的 [rc-form](https://github.com/react-component/form) 为例分析一下功能细节：

- 验证方式以 js 对象的验证为主，所以抽象了 async-validator 来定义 Rules, 这符合数据驱动的思想、而 dom 只是用作存取数据，不参与核心验证过程。
- 另外验证的细节和条件非常多：比如声明式的「验证规则、错误提示」、动态设定错误提示、自定义错误触发条件、要支持“只使用键盘”完成表单填写并按 enter 键能触发表单提交。

Tab 组件大概是所有移动 H5 应用的标配组件，而且这个组件跟 PC 的 Tab 组件应用环境很不一样。我们在 antd-mobile@1 版本里直接使用了 PC 的 rc-tabs, 结果用户反馈了很多问题，比如：

- 无法使用 Sticky 标签包裹选项卡头，即选项卡头不能 fix 到页面顶部或底部。
- Tab 选项卡作为路由跳转时不能和 react-router 组合使用，另外和 ListView 等其他组件组合使用也有问题。

以上问题在 [antd-mobile issues](https://github.com/ant-design/ant-design-mobile/issues/1287) 里有许多讨论，我们只能重新设计，比如改变了 TabTitle 和 TabPane 一一对应的关系、自动生成 panel 容器等，解决了以上诸多痛点问题。

其他组件，比如：

- Modal 的 dom 是否允许放到指定的某个元素里，还是统一放到 body 元素下？是放到 body 的末尾地方还是开始地方？
- Grid 组件与 Table 组件的区别？Table 组件是使用 table / tr / td 实现还是 div 实现？
- Notify / Message 类的组件是不是能统一做成一个组件？为什么不能。
- 弹窗类组件，是否必须有`destroy`方法？怎么提供出来。
- DatePicker / TimePicker, List 和 form control 组件等 共用 时的设计方式。

这里有很多的细节，需要权衡和确认、甚至要重新设计。看看都觉得好累啊 😂

## 总结

我觉得每一个合格的前端工程师，都要至少有开发几个组件、遇到并解决一些问题的经历，这些正是前端的基石和底盘呀。当然，我所说的都是错的~ 杂谈就谈到这里吧。😊

> 忘了重要事、欢迎大家来一起交流组件设计心得~

