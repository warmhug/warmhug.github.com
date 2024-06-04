---
layout: post
title: "ios上主屏幕webapp缓存问题分析"
tags: [js]
author_name: warmhug
---

现在的webapp类应用，打开后大都能看到个 “添加至主屏幕” 的向导性提示层（例如百度的许多产品、淘宝的部分产品），当用户添加后，便可以像native app那样，直接从主屏幕打开，而不用再打开浏览器再输入网址这样两步走的麻烦。但便捷的背后却藏着一些个人觉得不可忽视的用户体验问题。

问题描述如下：  
1、从主屏幕点开某个webapp，进入首页  
2、点击进去某个子页面  
3、切换到其他app，再进入此app，还能否看到原来的子页面了？  
正常的话，你从其他app再进入此webapp，会看到页面重新刷新，显示最初的首页内容，而你原来还没看完的子页面不会再显示了（ios5.1.1测试结果）。很多情况下，你不希望它去刷新（例如，你正在写许多文字写到一半，翻了好多页找到自己要的东西），不然你先前的工作就白费了。而这个问题在native app上是不存在的。

现在已经上线的产品，许多都没有解决这个问题，不知道是因为觉得这个问题可以忽略，或者是没有什么好的彻底的解决办法？面对这个，首先很可能会想着能否利用本地存储来解决；从网上查找一些别人的想法，也大都和web storage分不开，大致介绍如下：  
**1、offline application cache**    
缓存一些稳定的静态文件，不过个人觉得貌似对这个问题没有帮助，因为这个问题大概只需要记录离开时的状态。但是稍早的一篇文章对此问题做过挺深入研究并提出这个方法，这里摘出作者的问题描述和解决建议：  
But we see that the second time Untappd is launched from the home screen, all of the resources are re-requested. To make matters worse, none of these are Conditional GET requests, so a 200 status code is returned with the full response body. -- 即二次点击，所有资源都不从缓存里读，而是重新发送http请求。  
接下来看作者的“点睛之笔”：  
It’s unfortunate that home screen apps suffer from this bad caching behavior on the iPhone. Thankfully, there is a workaround: application cache. -- 即，解决方案是application cache。  
但是，作者又说...  
I feel bad about recommending the use of application cache. This is an issue with the browser cache on mobile Safari (and to a lesser degree on desktop Safari) that should be fixed. -- 实际上从别人项目经验中获悉，确实离线缓存的方案会有许多坑。  
（此分析详细请查看:[http://www.stevesouders.com/blog/2011/06/28/lack-of-caching-for-iphone-home-screen-apps/](http://www.stevesouders.com/blog/2011/06/28/lack-of-caching-for-iphone-home-screen-apps/)）。  
不过apple在其开发者文档中似乎很推崇webapp开发使用离线存储，这样除了能减少网络请求，还能在没有网络的情况下app还是可用的，与native app更相似。  
**2、localStorage 和 sessionStorage**   
每次进入某个页面后，标记这个页面的状态（一般可以是url即可），写入storage，这里觉得用sessionStorage 比用 localStorage 更合适，因为这个没必要长期存储，仍有待验证。更细节的添加和去除状态的时机判断，需要依据情况实现。    
**3、session 和 cookie**    
这个是后端结合前端的思路，后端写入会话状态，前端去读取状态，跳转到正确的页面。

解决办法不止以上讨论几种，聪明的你也许有更好的方案，希望能重视并实施起来解决它。  
这是个不大也不小的问题，试想：  
不管什么webapp产品的目标是什么？--吸引用户多访问多停留、由此才可能产生效益；  
用户停留久了，会有什么动作？--看到不明白的或想对比下的东西，去别的应用里搜索查找，或者会被突然到来的短信息打扰而去看信息，这些都会导致用户暂时离开。  
离开之后呢？--想回到原来的状态也回不去了，重新点来点去才能找到，或者辛苦输入的东西全部消失了。 

这的确是个问题，或者说是细节问题，但**细节决定成败，做好细节，是我们该追求的产品精神。**
