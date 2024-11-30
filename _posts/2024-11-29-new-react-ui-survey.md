---
layout: post
title: "react ui 基础组件库 新调研"
tags: [js]
author_name: warmhug
---

近些年可选的 react 组件库越来越多, 参考 [2023.stateofreact](https://2023.stateofreact.com/en-US/libraries/component-libraries/) 里的 component libraries 分类排名, 最“经典/成熟”的组件库依次为: [material-ui](https://mui.com/material-ui/) / [ant.design](https://ant.design) / [react-bootstrap](https://react-bootstrap.github.io/) 等. mui 和 antd(含rc底层组件) 诞生都已刚好超过十年之久, 应该是紧随 react 框架诞生的"最早和最完善"的组件库.

- 其中 mui 作为世一(react-ui库), 除了基本迭代外 也紧跟近几年流行的 headless UI 技术趋势, 抽象了 [base-ui](https://mui.com/base-ui/) 子组件库. 并跳出 material design 限制, 自创基于 joy design 的 [joy-ui](https://mui.com/joy-ui).
- 而 antd 作为世二, 在其 v5 版本里 全面使用 cssinjs, 抽象了更多 hooks, 也对细节 api 做了 `open variant` 等更标准化国际范的优化. 但是底层还未完成彻底重构, 离目前流行的 headless 趋势还有些距离.

流行榜单里还有 [ShadCN/UI](https://ui.shadcn.com/) / [Chakra UI](https://v2.chakra-ui.com/) 就是近几年围绕着 [tailwindcss](https://tailwindcss.com/) 体系火热起来的"新范式"的 UI 库. 另外还有其他更多的 UI 库, 也同样得到了越来越多的关注, 所有这些库 按类型可分为四类:

- CSS的扩展 Extensions of CSS: [Sass](https://sass-lang.com/), [Less](https://lesscss.org/), [Tailwind](https://tailwindcss.com/), [CSS Modules](https://github.com/css-modules/css-modules).
- 纯行为 Behavior libraries / Headless: [HeadlessUI](https://headlessui.com/), [Radix](https://www.radix-ui.com/), [React Aria](https://react-spectrum.adobe.com/react-aria/), for how things behave, not style.
- 样式系统 Style systems: [TailwindUI](https://tailwindui.com/), [DaisyUI](https://daisyui.com/), for a baked-in look and behavior that is up to the developer.
- 混合方式 Hybrid approaches: [ShadCN/UI](https://ui.shadcn.com/) ([awesome-shadcn-ui](https://github.com/birobirobiro/awesome-shadcn-ui)), [Chakra UI](https://v2.chakra-ui.com/), [primereact](https://primereact.org/), [KumaUI](https://www.kuma-ui.com/), [Magic UI](https://magicui.design/).
- 组件库 Component libraries: [MUI](https://mui.com/), [Ant Design](https://ant.design/), [Mantine](https://mantine.dev/), [Blueprint](https://blueprintjs.com/), [NextUI](https://nextui.org/docs/guide/introduction) [Fluent UI](https://fluent2.microsoft.design/).

> 其中 根据 Best of JS 网站对 2023 年新增 star 数量的分析, shadcn/ui 以 39.5k 的新增 star 数量位居榜首, 2023 年底已超过 44K Star, 到目前为止 目前已有 75K Star, 而它是在 2023 年 5 月开源, 可见短短时间里 得到了惊人的关注度.

### 怎么选?
> 不管做什么选择, 最通俗的标准应该都是能: 立足当下/面向未来.

综合需要根据 "业务需求/项目/人员" 现状, 并考虑到 "易用 实用 先进" 几个方面, 做具体分析:

- 易用性: 功能覆盖广, 组件和设计资源丰富, 文档详细, 社区活跃, 开箱即用, 快速上手开发, 能应对复杂场景.
- 实用性: 二次开发容易, 跨组件库的api命名和功能 能否一致, 存量项目"升级改造"成本低.
- 先进性: 符合技术未来发展趋势, 借力未来新技术社区生态, 保持人员技术新鲜度.

我们的业务类型, 大致可以概括为 "四表一局"(表格 表单 列表 图表 布局), 是比较典型的 后台管理 类型系统. 按照上边的考虑准则, 综合衡量其实还是 MUI / antd 最符合需求, 因为这些有"新兴范式"的组件库 都基于 "高灵活性 高定制化 完全自主掌控" 想法出发, 多少都会牺牲 开箱即用 的体验.

但目前需要从 antd 迁移到新的 react 组件库, 而且希望能尽量 无缝迁移, 剩下的选择就只有 MUI 或者新兴的几个组件库了. 经过对 以上列出的所有 备选库中 逐个做了 粗略了解, 并看了一些示例代码, 做以下推荐:

- 基于 [ShadCN/UI](https://ui.shadcn.com/) 二次封装, 对 antd api 做 port 适配.
  - 初始开发成本：较高 复杂场景需要额外开发.
  - 长期维护成本：较低 几乎没有外部依赖.
  - 灵活性: 高定制化 完全自主掌控.

- 选择 [MUI](https://mui.com/), 可靠性高, 但成本也较高.
  - Material Design UI比较难设计, Google设计语言 风格比较强烈，不易适配其他设计风格.
  - 组件封装较深, 内部实现复杂, 如果必须要 二次开发, 只能基于它提供的 [base-ui](https://mui.com/base-ui/) 进行。
  - 组件库 功能本身全面 可配置性高, 基本能解决所有业务需求, 但需要业务开发 直接使用 有新的学习成本.
  - 在 API 设计上与 antd 差异较大, 存量项目难以迁移, 只能并存.

- 选择 [Mantine](https://mantine.dev/) 或 [Chakra UI v2](https://v2.chakra-ui.com/)
  - 他们都有相对完善的功能, 比较容易做到 开箱即用, 二次开发成本 看起来相对不高.
  - 其中 Mantine 设计上与 antd 风格比较像, ui 风格变动应该较小. 也支持比较便利的 [form-validation](https://mantine.dev/form/validation/) 等功能.
  - 在 API 设计上与 antd 差异有不少, 但看起来并非很大.
  - 从这里看到 [Chakra UI v3](https://www.chakra-ui.com/blog/00-announcing-v3) 刚刚在 10 月份发布, 其中提到了受 ShadCN/UI 启发.

### 总结

具体成本的评估 还需要细化 并做出一些决定:

1. 对推荐的四个组件库, 都实际写个 比较完善的 Demo 或项目 进行尝试.
2. 最终确认 "是否需要二次开发, UI风格差异是否需强制统一" 等问题.
3. 如果选择 新范式 组件库, 额外考虑 tailwindcss 学习和使用的成本.


### 附录

### mui
视频: [基于 mui v5 的业务组件库](https://www.youtube.com/watch?v=iAkIWz4C-2w)

<img src="/assets/2024-11-29_mui-usage.jpg" style="width: 500px;" />

视频: [mui-base 介绍](https://www.youtube.com/watch?v=BVLBNqW4ves)

<img src="/assets/2024-11-29_mui-base.jpg" style="width: 500px;" />

### 参考 [ShadCN/UI](https://ui.shadcn.com/)

[中文](https://www.shadcn.com.cn/)

实例对比 https://codeparrot.ai/blogs/material-ui-vs-shadcn
https://go.lightnode.com/zh/tech/shadcn-ui

快速发展, 高度定制性, 现代化风格 商务稳重.
基于 Radix UI 和 Tailwind CSS，通过修改 Tailwind CSS 配置和覆盖样式来实现。
通过"复制粘贴"到你的项目中。这种设计方式颠覆了传统组件库的设计理念，但它的好处是更容易控制代码，决定组件的构建和样式。最大的灵活性和控制力. 考虑面向未来和先进性，如果项目注重快速定制化和紧跟现代 CSS 框架发展趋势，Shadcn/ui 是不错的选择.
将组件代码直接添加到项目中能够实现较高的自主控制程度。我们可以根据需要添加所需的样式和交互功能。对于某些情况来说，这样做确实可以提高效率，并降低代码的耦合度。举个例子，之前我使用过一个组件库，想要监听某个组件内的滚动，但是该组件并没有提供相应的方法，因此需要修改代码变得非常麻烦。这可能也是shadcn/ui组件库受到大量关注的原因之一，它具备这种灵活性。
另外比如 [anchor-positioning-api](https://developer.chrome.com/blog/anchor-positioning-api?hl=zh-cn) 这种新特性, 比较容易跟进.
https://juejin.cn/post/7320525947249803283
使用了，感觉要吐了，封装度太低了，和老板上午提需求，下午就要的精神背道而驰，完全不符合。
基于tailwind的UI库都这样，啥事件都没封装，效率不高，除了UI灵活一些.

需要高度自定义的 UI。团队熟悉 Tailwind CSS，且愿意花时间构建设计系统。
可能作者从一开始就没有考虑为纯粹的组件使用者们提供服务，而是为组件开发者提供了维护的便利。因此，如果我们站在组件使用者的角度来看，确实容易得出和自己不适配的结论。
Calendar 组件选用 React DayPicker；
Chart 组件选用 Recharts；
Table 组件选用 @tanstack/react-table；
Form 组件选用 react-hook-form 和 zod。
密码输入组件选用了 input-otp；
提示组件选用了 Sonner；
抽屉组件选用了 Vaul。
