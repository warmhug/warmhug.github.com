---
layout: post
title: "前后端通信方法"
tags: [js]
author_name: warmhug
---

> 文章内容大多来自学习摘记、实践思考。

## 跨域通信

- xhr1 无论是 get 或 post 都 **不能跨域**。

- xhr2 有两方面的增强：通信进度通知，跨域通信。
  - 它使用 [Cross-Origin Resource Sharing](https://developer.mozilla.org/en-US/docs/HTTP/Access_control_CORS#Simple_requests)，W3C 制定的跨站资源分享标准。post 前会产生一次 options 嗅探（称之为 preflight，简单请求就不会出现）来确认有否跨域请求的权限；客户端 post 时会带上 Origin 头指示来源网站，服务端响应时需带上 Access-Control-Allow-Origin 头与 Origin 的值匹配。
  - cors 实例，如下图（options嗅探）：
  - ![cors实例](https://t.alipayobjects.com/images/T1olFfXcpaXXXXXXXX.png)

- jsonp 跨域：JSONP 属于跨域 get，其之所以可行是利用了 script 标签的特性。
  - 实质是 script 的 src 里携带向服务器请求的 data，返回的 data 包裹在函数调用里，供数据处理函数使用。

- form + iframe (假无刷新)：通过 js 动态生成不可见表单和 iframe，将表单的 target 设为 iframe 的 name 以此通过 iframe 做 post 提交。
  - 提交后由于跨域，无法直接读取响应内容。一般的做法是，iframe 内通过 js 改变自身 location 的 fragment，外部则监听 iframe 的 onload 事件，读取 fragment 的内容。
  - iframe 的 src 改变时[事件侦听](http://stackoverflow.com/questions/2429045/iframe-src-change-event-detection)，`<iframe src="zz" onLoad="xx();"></iframe>`

- 设置 document.domain，来实现“跨子域”，但有[不好的地方](http://www.cnblogs.com/jkisjk/archive/2013/05/30/change_document_domain.html)

- flash 跨域：利用不可见的 swf 跨域 post 提交数据，需要部署 crossdomain.xml。若用户安装了 flash，则以此实现跨域通信。响应数据量较大时优势明显

- 页面有使用 `window.opener.xx()`之类、因为 opener 可以为任何域，所以存在`跨域`错误，利用`try..catch`捕获。

### 其他方法

- HTML5 postMessage
  - ie8及以上浏览器都支持，推荐使用。
- document.domain + iframe 的设置
- window.name 实现的跨域数据传输
- 利用 iframe 和 location.hash，会导致历史记录的产生，且数据量有限，同时，因为 URL 的内容可视，既不好看也容易泄露信息。
- 用服务端代理？虽然算是最“正宗”的完整跨域方案，但太麻烦了点——首先得有代理，如果量大的话，代理的负担会很重，会导致“瓶颈”制约。

### postMessage 跨域详解

otherWindow.postMessage(message, targetOrigin)

#### 示例

a.com/index.html 中的代码：

```html
<iframe id="ifr" src="b.com/index.html"></iframe>
<script>
  var ifr = document.getElementById('ifr');
  var targetOrigin = 'http://b.com';  // 若写成'http://b.com/c/proxy.html'效果一样
                                      // 若写成'http://c.com'就不会执行postMessage了
  ifr.contentWindow.postMessage('I was there!', targetOrigin);
</script>
```

b.com/index.html中的代码：

```js
window.addEventListener('message', function(event){
  // 通过origin属性判断消息来源地址
  if (event.origin == 'http://a.com') {
    alert(event.data);    // 弹出"I was there!"
    alert(event.source);  // 对a.com、index.html中window对象的引用，但由于同源策略，这里event.source不可以访问window对象
  }
}, false);
```

### 更多

- [JavaScript跨域总结与解决办法](http://www.cnblogs.com/rainman/archive/2011/02/20/1959325.html)
- [跨域iframe的高度自适应](http://www.cnblogs.com/snandy/p/3900016.html)

## 实时通信

- 轮询（定时发消息、每次都新建链接）、
- 长轮询（发消息后 等服务端返回、再发消息）、
- 基于流 http streaming（客户端与服务器端只保持一个持久的连接，低版本 IE 定时去监听 iframe 的 readystate 的变化、为 3 时向页面输出内容、为 4 时重新建立链接，达到不断轮询的目的）。

以上都需要“服务器端维持大量连接，开销很大”。相对来说 WebSocket 有以下优势：

- 服务器和客户端之间，可以双向的发送数据到对方，允许跨域。
- 你需要一台可以做 Event Loop 的服务器、支持第三方的 websocket 主机服务器，例如 Pusher 或者其它，浏览器和服务器只需要做一个握手的动作就形成了一条快速通道。

与 http 的区别？http 的通信只能由客户端发起，是半双工协议，在同一时刻数据只能单向流动，客户端向服务器发送请求(单向的)，然后服务器响应请求(单向的)。

更多参考：

- [HTTP DNS 等更基础知识](https://github.com/warmhug/backend/blob/master/network.md)
- [websockets 的问题](https://samsaffron.com/archive/2015/12/29/websockets-caution-required)、[JavaScript之web通信](https://segmentfault.com/a/1190000000423616)
