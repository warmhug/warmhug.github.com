---
layout: post
title: "Mobile web 基础知识点"
tags: [js]
author_name: warmhug
---

> 这里主要是做 antd-mobile 过程中的一些积累。
> 日期更改为 [antd-mobile@2.0](https://github.com/ant-design/ant-design-mobile/issues/1965) 发布时间，以此纪念！

### 屏幕尺寸

mm(毫米)=?px(像素): 两者没有转换关系，一个是绝对量，一个是相对量。比如15吋的显示器，你可以设置成 `1024*768` 像素，也可以设置成 `1280*960` 像素，可以有很多种。

像素就是CCD/CMOS上光电感应元件的数量，也即把影像放大数倍看到的小方点。作为图像的一种尺寸，只存在于电脑中，如同RGB色彩模式一样只存在于电脑中。像素是相对于显示器“屏幕分辨率”而言的，譬如，WONDOWS的用户所使用的分辨率一般是 96像素/英寸。而MAC的用户所使用的分辨率一般是 72像素/英寸。

屏幕分辨率是指屏幕显示的分辨率，显示分辨率就是屏幕上显示的像素个数，分辨率160×128的意思是水平方向含有像素数为160个，垂直方向像素数128个。屏幕尺寸一样的情况下，分辨率越高，显示效果就越精细和细腻。

> 影像分辨率单位 PPI(Pixel per Inch)，打印(打印机输出)分辨率单位是 DPI(dot per inch). 人眼能分辨出的最大分辨率是300dpi，超过这个分辨率，人的眼睛是无法看出差别的，也就是说 300dpi 和 600dpi 在人眼看来是没有差别的，所以现在的冲印设备最大的设计输出分辨率，就是300dpi。
> 显示器出厂时一般不标出表征显示器分辨率的 DPI 值，只给出点距，我们根据公式即可算出显示器的分辨率。

- iPhone 4/4s : 屏宽320. (物理像素 640 * 960. 2x. 对角线 3.5-inch. PPI 326)
- iPhone 5/5s/5c : 屏宽320. (物理像素 640 * 1136. 2x. 对角线 4-inch)
- iPhone 6/6s/7 : 标准模式屏宽375，放大模式屏宽320. (物理像素 750 * 1334. 2x. 对角线 4.7-inch. PPI 326)
- iPhone 6/6s/7 Plus : 标准模式屏宽414，放大模式屏宽375. (物理像素 1080 * 1920. 3x. 对角线 5.5-inch. PPI 401)

```js
console.log(window.devicePixelRatio) // retina 屏 = 2 (1px = 2 物理像素) 6plus = 3
// 物理像素 / 逻辑像素 = devicePixelRatio
console.log(window.innerWidth) // device-width
console.log(document.documentElement.clientWidth) // viewport width
console.log(Math.sqrt(Math.pow(640, 2) + Math.pow(960, 2)) / 3.5) // iphone 4 ppi
```

- [屏幕尺寸，分辨率，像素，PPI之间到底什么关系？](https://www.jianshu.com/p/c3387bcc4f6e)
  - 在同一个设备上，它的像素个数是固定的，这是厂商在出厂时就设置好了的。只有不同的设备之间，才有像素大小的区别。
- [Physical Pixels / Logical Pixels](https://developer.appway.com/screen/ShowSingleRecipe/selectedRecipeId/1429057950522)
  - [Are pixels in Photoshop logical or physical?](https://graphicdesign.stackexchange.com/questions/81055/are-pixels-in-photoshop-logical-or-physical/81083)
- [「像素」「渲染像素」以及「物理像素」](http://www.zhihu.com/question/27261444/answer/35898885)
- [css 单位标准](https://www.w3.org/TR/css3-values/#absolute-lengths)
- [Android 设备单位转换](https://pixplicity.com/dp-px-converter/)
- [Android 设备单位](https://stackoverflow.com/questions/2025282/what-is-the-difference-between-px-dip-dp-and-sp-on-android)
- [iOS 设备尺寸](http://iosres.com/)

大部分移动设备默认的 viewport 都是 980px (layout viewport) 大于可视宽度(visual viewport)，多数情况下要大于 device-width(即 ideal viewport width 理想宽度) ,我们一般都要在移动端重置 viewport，让`width=device-width`, 使 layout viewport 变成理想宽度，这样可以做到按页面宽度 100% 充满屏幕，水平不出现滚动条。

### Touch 事件兼容问题

- 在 Android 上 Touchmove 只触发一次，解决：阻止默认事件，在 start 或move 时，执行一次 e.preventDefault()
- 在 Android 上 页面滚动时，PageX/Y 并不包含滚动，需附加 scrollLeft/Top 修正
- 在 Android 上 在 a 标签上，move 后不触发 touchend 事件，a 标签的 href 属性从`javascript:void(0)`改为`javascript:;`

### Touch事件穿透问题 (Ghost Clicks)

click 事件在手机浏览器中的触发顺序：touchstart -> touchmove -> touchend -> click

浏览器在 touchend 之后会等待约 300ms 判断用户不是双击（double tap）行为，则触发 click 事件。
通过设置 meta 标签，禁止页面缩放，新浏览器不再需要等待 300ms [ref](https://webkit.org/blog/5610/more-responsive-tapping-on-ios/)。

如果绑定 touch 事件的 dom 元素在被点击触发后会隐藏、css3 transform 移走、requestAnimationFrame 移走等，而“隐藏、移走”后，它底下同一位置正好有一个 dom 元素绑定了 click 事件、或者有浏览器认为可以被点击有交互反应的 dom 元素（如 input 被点击有交互反应是获得焦点并弹起虚拟键盘），则会出现 **“点透”** 现象。

touch 事件在手机浏览器中的穿透问题，并不是由冒泡引起的，而是由于 click 事件延迟触发导致，所以通过阻止事件冒泡的方式解决穿透问题是不可行的。（iOS 系统通过在 touchend 事件里设置 `e.preventDefault()` 阻止浏览器默认事件可以解决，Android 不可行）

最好使用 Fastclick 处理 click 事件，能同时解决 300ms 延迟和点击穿透问题（[Fastclick如何解决穿透事件](http://www.cnblogs.com/yexiaochai/p/3442220.html)）。

> Fastclick 通过在 touchend 触发时，自己创建一个 click 事件并手动触发，替代了用户的 click 事件。

Touch 应用

- 手势 & 模拟 scroll & list & pullToRefresh & pullToLoadMore
  - <https://github.com/Lucifier129/pull-element>
  - <https://github.com/react-component/m-select-list>
- <https://github.com/react-component/m-picker/issues/43>
- [原生实现的 framework7 picker](https://github.com/framework7io/Framework7/blob/9f3589b2a2dc9d2119d41887a31309c8e811ec27/src/js/framework7/picker.js)
- [基于 angular 的 ionic picker](https://github.com/ionic-team/ionic/blob/1573043f4e31cabe1bed6bb0f4b171a8575d360d/src/components/picker/picker-column.ts)
- [基于 zepto 的 weui picker](https://github.com/Tencent/weui.js/blob/81deb5c1c3da3e7d4d5d124d4c5da100171b8408/src/picker/picker.js)
- [基于 react 的 weui picker](https://github.com/weui/react-weui/blob/f8b12cd6f383b3e07c64442623839376384e33d0/src/components/picker/picker.js)
- [基于 vue 的 饿了么 picker](https://github.com/ElemeFE/mint-ui/blob/28abcb96cd6f85fae0863b36ef8a24cacf7f721e/packages/picker/src/picker.vue)

### 高清方案 / 边线

方案来自 [antd-mobile](https://github.com/ant-design/ant-design-mobile/issues/683#issuecomment-270117203)、[anima-hd](http://gitlab.xx-inc.com/animajs/anima-hd)

- 为了便于计算, 通常让`1px == 0.01rem`即按 100 倍换算, 设计师按宽度 750px (iPhone 6 物理宽度, 2 * 理想宽度, [why?](https://www.zhihu.com/question/25308946/answer/32240185)) 做设计（适配到其他设备比例偏差不会太大），前端也按这个写 css 尺寸。那么：
  - dpr=1 `<html style="font-size: 50px">`, 设备: windows 电脑, mac (mar pro dpr为2).
  - dpr=2 `<html style="font-size: 100px">`, 设备: iPhone 4~6, Android.
  - dpr=2~4 `<html style="font-size: 100~200px">`, 设备: iPhone plus, Android Nexus 6P 等.
- 总结：
  - 根据不同设备dpr、去改变 html 元素上的 font-size 值、用 rem 能方便实现“元素尺寸等比缩放”效果。
    - 缺点：依赖的第三方组件，js内联px高度设置，iframe 等都需要适配。
  - 根据不同设备dpr、缩小 viewport scale 设置、自然支持 1px 物理像素的“细腻边线”效果。
    - 缺点：地图使用 canvas 绘制，而 canvas 的本质是位图，缩放后会糊掉。
  - 对于“横屏”或“大屏”设备、需要支持显示更多内容，适配规则：文字流式，控件弹性，图片等比缩放。
    - 控件弹性指的是，navigation、cell、bar等适配过程中垂直方向上高度不变；水平方向宽度变化时，通过调整元素间距或元素右对齐的方式实现自适应。这样屏幕越大，在垂直方向上可以显示更多内容，发挥大屏幕的优势。
    - 具体做法：不能全用 rem, 需要不变的地方用 px 代替 rem 。
- Android 上小于 1px 的边线、会变为 0px 而不能显示， iOS8 之后支持 0.5px 宽度。
  - 在代码里把`1px`改写成`1PX`, 使用`postcss-pxtorem`工具时, `1PX`的写法能避免被转为`0.01rem`, 并且`1PX`是能正常被浏览器渲染的(不区分大小写)。
  - [rem 产生的小数像素问题](http://taobaofed.org/blog/2015/11/04/mobile-rem-problem/)
- [Retina屏的移动设备如何实现真正1px的线？](http://jinlong.github.io/2015/05/24/css-retina-hairlines/)
- [1px 边线方案总结](https://www.w3cplus.com/css/fix-1px-for-retina.html)
  - ref: <https://ariya.io/2011/08/mobile-web-logical-pixel-vs-physical-pixel>

### 细节问题

页面横向能拖动问题：注意哪里设置了 width 之和为 100% ，但没设置 `box-sizing: border-box;`

使用 <https://github.com/kisenka/svg-sprite-loader> 做 svg icon 图片部分 svg 渲染不出来：

- <https://github.com/kisenka/svg-sprite-loader/issues/53> 去掉 svg file 里的 mask
- 或者 改变默认样式设置 <https://github.com/kisenka/svg-sprite-loader/issues/59> `visibility:hidden`改为`display:none`

```html
<!-- label 兼容性问题 -->
<label><input type="checkbox" />点击我 input 能被选中</label>
<label><input type="checkbox" /><span>点击我 input 不能被选中，因为有 span 包括</span></label>
此问题出现在包括 iOS 10 在内的大多数手机系统里；另外部分手机会出现点击选中延迟感严重的问题
```

```css
/* 解决 transform 闪动问题：*/
-webkit-backface-visibility : hidden;
-webkit-transform-style : preserve-3d;
-webkit-transform : translate3d(0,0,0);
```

### 更多

- [在做 iOS 和 Android 的 HTML5 开发时，你都掉到过哪些坑里？](https://www.zhihu.com/question/34556725)
- [intersectionobserver](https://developers.google.com/web/updates/2016/04/intersectionobserver)
- [`passive: false`](https://github.com/react-component/m-pull-to-refresh/commit/96474e3a6f9af544b9ad87fe4b7211b274ad1027)
