---
layout: post
title: "前端组件设计杂谈"
tags: [js]
author_name: warmhug
---

前端发展到今天，组件化的概念已经深入人心，特别是 react 得到广泛应用以来，应该没有哪一个从业者会说自己从没写过 component 了！而自己这几年的工作、除了业务支持外主要的技术积淀、也都围绕着 ant design (mobile) 前端组件库。个人早期参与的是 PC 组件的建设、之前也专门针对 Tree / TreeSelect 组件总结了一些[开发心得](http://warmhug.github.io/2016/07/23/react-components-tree-tree-select.html)，近两三年主要参与建立起了 [mobile](https://github.com/ant-design/ant-design-mobile) 组件库。

PC 和 Mobile 组件开发大多地方都是类似的，主要的区别体现在前者是本身功能复杂、后者是运行环境复杂。本文涵盖了 PC 和 Mobile 组件，主要分析一些实际的 case 以及相应的设计方式，又因为主要是杂谈、所以不是很有“起承转合”的严谨工整文采风格~ 那么我们就开始聊聊吧！😁

### 还记得“早期经典”的组件设计吗

1). 创建一个组件类

```js
function TreeView() {
}
```

2). 设置组件类的配置项或属性

```js
function TreeView(config) {
  this.cfg = extend({}, config);
}
```

> 另外：组件的属性 只能通过方法来访问；组件的属性 value change 后组件自动映射变化。

3). 组件类上附加组件方法

```js
TreeView.prototype.xx = function () { };
```

4). 为组件添加自定义事件

mixin 进来带有 on / off / fire 等方法的事件系统，为组件级别添加事件，屏蔽底层的 dom 事件，方便使用。

5). 抽出 Widget 抽象类，作用是为 ui 组件提供统一的接口名，统一生命周期管理。

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
  - 如[validate.js](https://github.com/ansman/validate.js)。
  - 参考 ng 里针对 `ng-model="xx"` 绑定的 xx 数据进行验证，校验结果自动反馈到相应 dom 上。
- 另外验证的细节和条件非常多：比如声明式的「验证规则、错误提示」、动态设定错误提示、自定义错误触发条件、要支持“只使用键盘”完成表单填写并按 enter 键能触发表单提交。
  - 如 Ajax 返回后根据错误码手动设置错误提示。
  - 如 required 验证对于文本框默认为空时触发，对于“多选框”验证时，需要验证选择后的数据数组长度是否为零时触发，与文本框不同。

Tab 组件大概是所有移动 H5 应用的标配组件，而且这个组件跟 PC 的 Tab 组件应用环境很不一样。我们在 antd-mobile@1 版本里直接使用了 PC 的 rc-tabs, 结果用户反馈了很多问题，比如：

- 无法使用 Sticky 标签包裹选项卡头，即选项卡头不能 fix 到页面顶部或底部。
- Tab 选项卡作为路由跳转时不能和 react-router 组合使用，另外和 ListView 等其他组件组合使用也有问题。

以上问题在 [antd-mobile issues](https://github.com/ant-design/ant-design-mobile/issues/1287) 里有许多讨论，我们只能重新设计，比如改变了 TabTitle 和 TabPane 一一对应的关系、自动生成 panel 容器等，解决了以上诸多痛点问题。另外还有许多通用问题如 tab 切换后，前一个页面的 Ajax 请求还没完成，要不要 abort 掉？要考虑怎么解决。

其他组件，比如：

- Tree 组件实现功能：节点展开收起、单选/多选、选中/半选/不选、异步加载、右键菜单、节点增删改查、节点拖动。这些都涉及到复杂的交互和树状数据遍历修改问题。
- Modal 的 dom 是否允许放到指定的某个元素里，还是统一放到 body 元素下？是放到 body 的末尾地方还是开始地方？末尾。背景层 backdrop/mask，有的直接放在 body 下和 modal 框平级，有的放到 modal-container 里边(感觉更好)
- Grid 组件与 Table 组件的区别？Table 组件是使用 table / tr / td 实现还是 div 实现？
  - Grid（[datagrid](https://github.com/zippyui/react-datagrid)），使用 div 不用 table/tr/td，支持可变列数、筛选、排序、列宽度拖动改变、单选多选、分页、行列单元格自定义样式。
  - Table 组件，使用 table / tr / td 用于简单表格展示。
- Notify / Message 类的组件是不是能统一做成一个组件？为什么不能。
  - 应该区分成两类。一类：点击某个按钮，或操作成功失败时，页面某个位置出现的弹出框。（例如顶部或右上角弹出的通知）二类：页面最顶部固定的一行横通知条。（例如系统维护通知）
- 弹窗类组件，是否必须有`destroy`方法？怎么提供出来。
- DatePicker / TimePicker, List 和 form control 组件等 共用 时的设计方式。
  - TimePicker 搜索当天，从 00:00:00 到 23:59:59；限定可以选择的时间范围，最小时间、最大时间(例如最小时间是now，最大时间如果不设置、那默认是多少?)
  - DatePicker / TimePicker 共用一个时间点时，之间的影响（例如datepicker选完日期，可能会重置时间为 00:00，影响 TimePicker 怎么处理?）
- Select 组件：支持 Infinite scroll
- Select / tags-input 组件
  - 特殊需求：在 [tags input select](http://react-component.github.io/select/examples/tags.html) 里，想直接输入一串如 `name1,name2,name3,name4,nam5,....` 这样比较长的内容（预先准备好的人员列表），组件自动根据分隔符(例如这里的逗号)，split 出来，然后分别验证这些条目是否合法（如果分别发Ajax将会很多、不可行），再转换为一个个 tag。 这种需求一般是单独做一个「导入」文件或组的功能来实现，但放到 tags-input 组件或 select 组件里，感觉也有一定合理性。 在 select2 的 [Automatic tokenization](https://select2.github.io/examples.html#tokenizer) example 里输入 `red,blue,green,`  这串特定分割的字符串，会自动根据逗号 split 出三个 tag 出来。select2 这个还能输入更多自动保存到选择list里，也不涉及到发Ajax操作。
- Pagination 组件，如图 ![page](https://t.alipayobjects.com/images/rmsweb/T1OIlhXmVcXXXXXXXX.png) 如果数据总数50万+时，每页显示5个页码，下拉框就会有10万条目，导致页面dom数过大，浏览器崩溃。

另外关于 **API 设计和动画相关问题举例**：一些命令式 API 比如 `Popup.show(<Component />)` 的使用限制：假如 A 组件的 click 方法里 调用了 `Popup.show()` ，最后会需要使用类似（此处故意加 res）`const res = ReactDOM.render(<Component />， new_Mount_Node)`，这会在新的 new_Mount_Node 根节点渲染 Component 组件，这与调用者 A 组件 所在的 mount 根节点 不是同一个。
如果 A 组件所在组件树上有 新的数据，要更新 Component ，此时需要重新调用 `Popup.show()` 方法、即需要重新执行上述 `ReactDOM.render` 代码来进行更新。
但是 `Popup.show()` 方法调用时会有入场动画等，更新数据时不再需要显示动画，可以考虑加上 `Popup.update()` 方法，也需要重新调用`ReactDOM.render`，如果动画是做在 Component 组件里边，同样还会有动画，无法解决。
那是否能通过`ReactDOM.render`的返回值 res 来获取到 Component 手动调用什么方法进行更新呢？没方法能做到。最终解决办法：hack 方式、在更新数据时去掉动画。-- 总结，对于这种命令式 API , 最好是只传入简单字符串，如果传入复杂组件，组件自己维护状态、跟调用者的状态关系尽量少。

这里有很多的细节，需要权衡和确认、甚至要重新设计。看看都觉得好累啊 😂

## 总结

我觉得每一个合格的前端工程师，都要至少有开发几个组件、遇到并解决一些问题的经历，这些正是前端的基石和底盘呀。当然，我所说的都是错的~ 杂谈就谈到这里吧。😊

> 忘了重要事、欢迎大家来一起交流组件设计心得~

### 引用

- [front-end-application-frameworks-component-architectures](http://developer.telerik.com/featured/front-end-application-frameworks-component-architectures/)
- [javascript组件化](http://purplebamboo.github.io/2015/03/16/javascript-component/)
- [如何实现一个 mvvm 组件](http://shepherdwind.com/2014/05/17/how-to-reliaze-mvvm--bidi/)
