---
layout: post
title: "react ui 基础组件库 新调研"
tags: [js]
author_name: warmhug
---

近些年可选的 react 组件库越来越多, 参考 [2023.stateofreact](https://2023.stateofreact.com/en-US/libraries/component-libraries/) 里的 component libraries 分类排名, 最“经典/成熟”的组件库依次为: [material-ui](https://mui.com/material-ui/) / [ant.design](https://ant.design) / [react-bootstrap](https://react-bootstrap.github.io/) 等. mui 和 antd(含rc底层组件) 诞生都已刚好超过十年之久, 应该是紧随 react 框架诞生的"最早和最完善"的组件库.

- 其中 mui 作为世一(react-ui库), 除了基本迭代外 也紧跟近几年流行的 headless UI 技术趋势, 抽象了 [base-ui](https://mui.com/base-ui/) 子组件库. 并跳出 material design 限制, 自创基于 joy design 的 [joy-ui](https://mui.com/joy-ui).
- 而 antd 作为世二, 在其 v5 版本里 全面使用 cssinjs, 抽象了更多 hooks, 也对细节 api 做了 `open variant` 等更标准化国际范的优化. 但是底层还未完成彻底重构, 离目前流行的 headless 趋势还有些距离.

流行榜单里还有 [ShadCN-UI](https://ui.shadcn.com/) / [HeadlessUI](https://headlessui.com/) 就是近几年围绕着 [tailwindcss](https://tailwindcss.com/) 体系火热起来的"新范式"的 UI 库. 另外还有其他更多的 UI 库, 也同样得到了越来越多的关注, 所有这些库 按类型可分为四类:

- CSS的扩展 Extensions of CSS: [Sass](https://sass-lang.com/), [Less](https://lesscss.org/), [Tailwind](https://tailwindcss.com/), [CSS Modules](https://github.com/css-modules/css-modules).
- 样式系统 Style systems: [TailwindUI](https://tailwindui.com/), [DaisyUI](https://daisyui.com/), for a baked-in look and behavior that is up to the developer.
- 纯行为 Behavior libraries / Headless: [HeadlessUI](https://headlessui.com/), [Radix](https://www.radix-ui.com/), [React Aria](https://react-spectrum.adobe.com/react-aria/), for how things behave, not style.
- 混合方式 Hybrid approaches: [ShadCN/UI](https://ui.shadcn.com/) ([awesome-shadcn-ui](https://github.com/birobirobiro/awesome-shadcn-ui)), [primereact](https://primereact.org/)(GitHub), [KumaUI](https://www.kuma-ui.com/), [Magic UI](https://magicui.design/).
- 组件库 Component libraries: [MUI](https://mui.com/material-ui/), [Ant Design](https://ant.design/), [Mantine](https://mantine.dev/)(个人维护), [Chakra UI v2](https://v2.chakra-ui.com/), [NextUI](https://nextui.org/docs/guide/introduction)(nextjs团队), [Fluent UI](https://fluent2.microsoft.design/)(微软), [Blueprint](https://blueprintjs.com/).

> 其中 根据 Best of JS 网站对 2023 年新增 star 数量的分析, shadcn/ui 以 39.5k 的新增 star 数量位居榜首, 2023 年底已超过 44K Star. 到目前为止 目前已有 75K Star, 而它是在 2023 年 5 月开源, 可见短短时间里 得到了惊人的关注度, 仍在快速发展中.

## 推荐选项

综合需要根据 "业务需求/项目/人员" 现状, 并考虑到 "易用 实用 先进" 几个方面, 做具体分析:

- 易用性: 功能覆盖广, 组件和设计资源丰富, 文档详细, 社区活跃, 开箱即用, 快速上手开发, 能应对复杂场景.
- 实用性: 二次开发容易, 跨组件库的api命名和功能 能否一致, 存量项目"升级改造"成本低.
- 先进性: 符合技术未来发展趋势, 借力未来新技术社区生态, 保持人员技术新鲜度.

我们的业务类型, 大致可以概括为 "四表一局"(表格 表单 列表 图表 布局), 是比较典型的 后台管理 类型系统. 按照上边的考虑准则, 综合衡量其实还是 MUI / antd **最符合需求**, 因为这些有"新兴范式"的组件库 都基于 "高灵活性 高定制化 完全自主掌控" 想法出发, 多少都会 **牺牲开箱即用** 的体验.

希望能够兼容 antd 方便业务**无缝迁移**, 也要能立足当下/面向未来. 从以上提到的"各类UI库"中筛选出来以下几个.

### [ShadCN-UI](https://ui.shadcn.com/) / [HeadlessUI](https://headlessui.com/) 体系

特点

- 初始开发成本：较高. 长期维护成本：较低. 灵活性: 高定制化 完全自主掌控.
- 适用于: 需要高度自定义的 UI, 团队熟悉 Tailwind CSS，且愿意花时间构建设计系统.
- ShadCN/UI 本身 基于 Radix-UI 和 Tailwind CSS，通过修改 Tailwind CSS 配置和覆盖样式来实现.
- Radix-UI 所有组件的 api [相似](https://www.radix-ui.com/primitives/docs/overview/introduction), 学习成本低.

优势

- 各平台(含mobile/RN)兼容性, 跨端响应式(tailwind), aria(Accessibility), 完全可定制, 后期可复用性 (form table 功能完备 / 技术架构先进 / 国际化社区 / AI).
- 提供 cli [init / add](https://ui.shadcn.com/docs/cli) 或者复制粘贴 等方式添加组件, 能做成类似 [https://ui.shadcn.com/blocks](https://ui.shadcn.com/blocks) 的"区块"平台 (另可考虑结合 [https://bit.dev](https://bit.dev) 功能).


### MUI / Mantine / Chakra UI v2 系列

[MUI](https://mui.com/) 特点

- Google Material Design 风格比较强烈 比较难设计, 不易适配其他设计风格. 修改UI的成本**预计较高**.
- 当前稳定版本的组件封装较深, 内部实现比较复杂. 其中复杂组件 data-table date-picker tree 等的高级版本, 属于**收费项目**. 总体**功能全面/可配置性高**, 基本能解决目前所有业务需求.
- 新的 [base-ui](https://mui.com/base-ui/) 是在 24年初 规划启动([base-ui规划](https://mui.com/blog/base-ui-2024-plans/)), 整体方案是按照 HeadlessUI 新范式进行, 目前进度比较缓慢 处于**不可用**状态.

[Chakra UI v2](https://v2.chakra-ui.com/) / [Mantine](https://mantine.dev/) 特点

- chakra-ui 和 mantine 是过去几年除了 mui / antd 外的 **功能相对完善/可开箱即用** 的首选组件库.
- mantine 样式方案和hooks **自成一体**, 其中的 [form-validation](https://mantine.dev/form/validation/) 部分与 antd 比较类似. 但整个仓库 长期由一个人维护 **单点故障** 的风险大, 技术 **排他性** 过大.
- chakra-ui 刚刚在 10 月份发布了 [v3](https://www.chakra-ui.com/blog/00-announcing-v3) 版本 (另参考[contributing](https://www.chakra-ui.com/docs/get-started/contributing)), 文章中提到受了 ShadCN/UI 启发. 做了**彻底**的重构, 最底层采用了 **状态机** 方式实现了单独的 [zagjs](https://zagjs.com/) 专注于组件功能逻辑的库 能兼容 react/vue 等各类框架, 上层的 [ark-ui](https://ark-ui.com/) 对应 HeadlessUI 部分, 再上层参考 Park UI 的 design system 和 Panda 的 styling engine 和 Radix Themes 和 Shadcn 的 cli, 最终组合成了新的 chakra-ui v3 , 显然是集合了 **百家所长** 但这也搞得**太过复杂**, 真的还有时间能实现 tree table 等复杂组件的功能吗?

优势: 他们都在持续做一些**很创新**的优化重构, 在**技术架构**上的参考价值非常高.

不合适点: 这几个组件库在 API 设计上与 antd 差异都比较大, **修改和二次开发** 成本均很高.

### best-core

特点

- 看 changelog 最早是 2019-07 开始发版, 当时对应的是 antd@3 版本. 最新的大版本 v5 是从 2022-04 发版, 应该对应的 antd@4 版本, 因为 antd@5 是 2022-10 发布的.
- 主要的 api 实现跟 antd 一致, 部分看起来参考了 mui 等 api 设计方式 (但感觉没学到精髓~). 从 v3 版本开始, 采用 cssinjs 的方案.
- 对比 antd 几乎每个组件都有不一致地方, 总体预估有 30% 左右的 api或功能 不一致.

优势: 预估与 antd 保持了约 70% 的相似度.

不合适点: cssinjs 的方式 带来改造成本, 组件api 设计缺少一惯性 不可预测. 比如 button 相关的 style 设计 对比 [material-ui-button](https://mui.com/material-ui/react-button/) , 它没参考 Material Design 设计文档中提到的 variant 规范.


### 小结

- 成本最低/综合最优: 采用 shadui + tailwindcss 的"新范式", **重构**部分轻型简单组件. **复杂组件 继续使用 antd 相应的组件**, 对代码进行部分重构修改 使之合规.
  - 业务收益: 可在业务中直接使用 tailwindcss 原子样式, 禁止自由发挥的 style 写法, **使业务 UI 更加统一和标准化**.
  - 先进性: 紧跟国际最前沿的发展趋势, 借力未来 AI 大模型的巨大潜力, 可能达到 **完全的智能化** 生成代码.
- 成本次低/无其他优势: 基于 best-core 组件库, 进行上层封装, 抹平功能和 api 差异.

以下分别根据 shadui 和 best-core 来粗略评估工作量.

## 工作量

基于 best 和 shad 与 antd 对应的每个组件的 "功能/UI/API" 差异, 考虑到 "开发/适配/兼容/额外学习成本" 等因素.
时间按 人天 估算, 需要 100% 投入度.

### 复杂组件和配置

| 模块        | best-core  |  shad + antd | 纯 shad 体系 |
| ----       | ----  | ---- | ---- |
| theme      | 7   |  15    | 15 |
| icon       | 5   |  3    | 7  |
| layout相关  | 5   |  3    | 10  |
| config/i18n | 5   |  3    | 10  |
| animation  | 3   |  6    | 10  |
| form       | 9   |  3    | 18 基于 [react-hook-form](https://react-hook-form.com/)和[zod](https://zod.dev/)封装      |
| input类    | 9   |  3    | 18  |
| table      | 12  |  3    | 20 基于 [tanstack/table](https://tanstack.com/table) 封装 |
| date       | 6   |  3    | 12 |
| pop弹窗类   | 10   |  3   | 15 基于 [floating-ui](https://floating-ui.com/) 封装 |
| select/Combobox | 6   |  3  | 15 |
| tree       | 5   |  3    | 12 内置无此组件 |
| tabs       | 6   |  3    | 12 |
| Carousel   | 6   |  3    | 12 |
| menu   | 6   |  3    | 10 |

### 简单组件

| 模块        | best-core  |  shad + antd | 纯 shad 体系 |
| ----       | ----  | ---- | ---- |
| button 系列     |  5  |  3    | 10 没有点击效果 |
| Alert / card / Badge / Empty / Result / Spin  |  10  |  8  | 10  |
| collapse / cascader / Transfer   |  10   |  6    | 15  |
| anchor / BackTop / Progress / Skeleton  |  7   |  3   | 14  |

### 小结

| 模块        | best-core  |  shad + antd | 纯 shad 体系 |
| ----       | ----  | ---- | ---- |
| 合计工作量(人天)  |  132  |  1. 80 : UI 类组件都切换成 tailwind 模式. (功能类组件 & UI组件的功能 保持不变) 2. 40 : 保持不变, 纯合规改造.
方式一: 每个组件代码约有 30% 的变更, 额外会抽象出 可在业务中直接使用 tailwindcss 原子样式.
方式二: 每个组件代码约有 10% 的变更, 主要进行 rc 等组件整合, 并改造使之合规.     | 245 |


## 附录

视频: [mui-base 介绍](https://www.youtube.com/watch?v=BVLBNqW4ves)
<img src="/assets/2024-11-29_mui-base.jpg" style="width: 200px;" />

视频: [基于 mui v5 的业务组件库](https://www.youtube.com/watch?v=iAkIWz4C-2w)
<img src="/assets/2024-11-29_mui-usage.jpg" style="width: 200px;" />
