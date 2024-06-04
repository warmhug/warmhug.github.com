---
layout: post
title: "Ant Design Mobile 预发布日志"
tags: [js]
author_name: warmhug
---

一年多前，我们发布了 Ant Design，经过一年多发展，Ant Design 已经在[蚂蚁内外](https://github.com/ant-design/ant-design/issues/477)得到了非常广泛的应用。但相对 Material Design / Bootstrap / MetroUI 等我们是不是还少点什么呢？没错，是少了这个移动时代里必须的针对 Mobile 的设计和实现。所以，正值 Ant Design (pc) 发布 1.0 之际，我们的 Ant Design Mobile 也终于要跟大家见面了！

![](http://024028.oss-cn-hangzhou-zmf.aliyuncs.com/uploads/react-ui/ant-mobile/6fd989029b9204258638b380c4165402/image.png)

## 设计

随着蚂蚁金服中后台的移动端需求增多，不同的设计规范及研发方式，给设计者和开发者带来大量的重复工作，降低了产品的研发效率。

我们希望为设计者和开发者提供一套统一的设计规范，可以降低设计、开发成本，减少沟通误差。不同于大多数设计规范，我们抽象出通用组件的基础样式和组件属性，使得拓展性更强。基于这套规范我们可以快速实现页面，提高研发效率。

## 实现

我们代码库前期是基于“钱包H5规范库 - AntUI”。半年多前，上海团队同学做了 antui-react 版本的实现，同时我们团队做[底层基础组件](https://github.com/react-component)的支持。近一个月又有更多同学加入进来，一起共建。所以这不是我们一个团队或一小部分人YY的东西，而是多团队一起合作、大家都想做的东西，我们值得期待：她会变得更好。目前，已经实现了这些内容：

- 移动端常用的基础 ui 组件（截图只是部分组件）
![](http://024028.oss-cn-hangzhou-zmf.aliyuncs.com/uploads/react-ui/ant-mobile/3db01259351c0635dcdaebbdffabeeb0/image.png)
- 组件更贴近 react-native 的使用方式，更贴合移动开发同学口味。如APIS里的组件和 native 组件 API 及用法都比较类似。
- 每个组件都能做到`按需加载`，避免了一次性加载所有组件、造成的移动端性能压力。

## 快速使用

```js
$ tnpm install @alipay/antm-init -g
$ mkdir antm-demo && cd antm-demo
$ antm-init
$ tnpm install

import { ListDatePicker } from '@alipay/antm';
React.render(<ListDatePicker />, mountNode);
```

## 未来计划

- 下个版本 0.6.0 roadmap
- 继续进行组件的设计与实现上的完善
- 增加一系列设计模式，覆盖常用的业务场景
- 纳入 react-native 组件，除了目前 web 端外也将覆盖 iOS / Android 端
- 在合适时机会开源、让外部广大社区帮忙一起完善

## 参与
> 和 antd pc 一样，诚诚诚邀大爷们来玩啊~

- 欢迎各类项目试用，欢迎各类 建议和试用反馈
- 欢迎更多专业移动端开发参与贡献
- 旺旺交流群 1639845850

