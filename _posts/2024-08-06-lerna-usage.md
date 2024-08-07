---
layout: post
title: "lerna 实践"
tags: [js]
author_name: warmhug
---

最近团队在做一个类似 [ant-design/pro-components](https://github.com/ant-design/pro-components) 的组件库，便 fork 了这里的代码。一是要把底层依赖比如 antd 修改为内部的基础组件库，二是需要使用公司内部的“研发和发布流程”、其中不少改动都跟 lerna 有关，为了彻底搞清楚它的用法、也看了一遍它的核心代码，总结了一些实践出来。

使用
[lerna](https://lerna.js.org/docs/recipes/using-pnpm-with-lerna) 和
[pnpm](https://pnpm.io/workspaces)
实现 monorepo 的管理。lerna 的原理及主要流程如下：

模仿或直接调用 `git commit/diff/tag` 、 `npm publish` 、 `semver` 、`conventional-bump` 、 `conventional-changelog` 等功能，监测代码变动，自动设置各个子包及其依赖包的“版本号”、并根据 git commit 的信息(符合提交规范)自动生成 changelog 文档。

所以 lerna 的核心部分就是 `lerna version` 和 `lerna publish` 这两块。其他辅助的命令比如 `lerna list` 作为工具也非常有用。如果要深入掌握 lerna 就需要对最核心的 **version 和 publish** 命令，以及它们的生命周期有所了解。

在使用 lerna cli 时制作自定义脚本时，就很可能需要利用它的一些生命周期钩子。其中核心的有这些 `(pre|post)version / prepare / prepack / postpack / postpublish`，最早和最晚执行的分别是 `preversion | postpublish`。 其中关键的地方涉及到“代码的变更和npm的发布”：

- 修改和提交代码：在 postversion 钩子之前执行`gitCommit`([源码](https://github.com/lerna/lerna/blob/main/libs/commands/version/src/index.ts#L881))，之后执行`gitPush`。整体都是在 version 阶段里。
- 推送 npm 并打 dist-tag 标记：在 postpublish 钩子之后执行 `npmDistTag`([源码](https://github.com/lerna/lerna/blob/main/libs/commands/publish/src/index.ts#L1089))。整体都是在 publish 阶段里。

### 业务特殊场景

公司里发布 npm 包，一般通过 “研发流水线+云构建” 的方式进行，比如支持运行 `build.sh` 脚本，在这个脚本里根据 流水线的 test/prod 环境变量，调用 `npm/lerna publish` 来分别生成 beta或prod 的 npm 包。

> 但是公司为了源代码安全，**不允许流水线直接提交代码到 gitlab 仓库里**。所以在流水线的“构建脚本”里无法自动把 `lerna version` 生成 commit / tag / changelog 内容提交到 gitlab 里。

所以，我们在 test 环境流水线里只发布 beta 版本：

```sh
lerna publish prerelease --preid beta --dist-tag beta --no-push --no-git-tag-version --yes
```

以上命令其实是 `lerna version prerelease --preid beta` + `lerna publish` 两个功能的组合，prerelease 会把有变更的包升级成 beta 版本号，然后 publish 到 npm 上。
注意：这里不会生成 changelog 因为没有 `--conventional-commits` 参数，因为设置了 `--no-git-tag-version` 也就不会改变 package.json 的 version 不打 git tag，但也可以不设置、因为这些内容不会被推送到 gitlab 里。

针对测试环境的 beta 代码，只需要发布到 npm 即可，其实没必要在 gitlab 里有 tag changelog 等发布记录。但**正式版本需要这些**，那么在正式环境流水线发布正式版本时、应该怎么做呢？

核心做法就是提前 **检测 gitlab 的 tag 和 commit 信息** 是否存在。如果存在并检测通过，就继续运行如下发布命令，否则就终止抛错、流水线提示运行失败。

```sh
lerna publish from-package --no-push --no-git-tag-version --yes
```

以上正式版本的发布命令和beta版本最大的区别是 **from-package** 这个配置！
设置 from-package 后、lerna 会先拉取待发布包在 npm 上的已有版本号信息，如果发现 npm 上已经存在此版本号(即使包的代码有改变) 则返回 No changed packages to publish。如果没有则进行发布，而且不会连带发依赖这个包的包。
差不多就是只运行 `npm publish` 命令的意思，其他的 tag changelog 等事情、需要你提前自己做好，怎么做呢？运行如下命令即可：

```sh
lerna version patch --exact --message 'chore(release): Publish' --conventional-commits
```

### 总结

至此、总算解决了以上提到的各种问题，但稍作思考会发现 lerna 最适合在 GitHub 这种**完全开源开放**的地方、这会充分发挥其各项能力。而在公司内仅仅就因为不能在脚本里推送代码这一个点，就导致其实用性大打折扣。另外、必须 “严格遵守 semver 版本更新规范、commit 提交规范” 之后，才能真正地把它用起来。

最后附带下常用命令如下：

```sh
# https://github.com/lerna/lerna/tree/main/libs/commands/version
# https://github.com/lerna/lerna/tree/main/libs/commands/publish
lerna list -l --graph
lerna diff

# 所有子包的版本 不管是不是正式版 都升级为 beta 版
lerna version prerelease --preid beta

lerna publish patch
lerna version --conventional-commits

lerna publish from-package --yes
# 以下同时传入 from-package --canary 只有前者生效，参考 lerna publish 代码逻辑
lerna publish from-package --canary --preid beta
# 如下 多了个 canary 则 生成的版本号带有 hash 原因参考 lerna publish 代码逻辑
lerna publish --canary prerelease --preid beta --dist-tag beta
```
