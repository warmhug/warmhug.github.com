---
layout: post
title: "接上篇--主屏幕webapp问题再研究"
tags: [js]
author_name: warmhug
---

先简介下上篇文章内容，《ios上主屏幕webapp缓存问题分析》[http://warmhug.github.io/2013/04/21/ios_homeScreenApps_cache_problems.html](http://warmhug.github.io/2013/04/21/ios_homeScreenApps_cache_problems.html)，主要介绍一个问题：**从主屏幕的图标点击进入某个webapp页面，然后切换到其他程序，再切换回来，页面会重新刷新**，相应的给出了利用web storage和session、cookie的解决思路。虽然没有真正用代码实现，但依据思路，实现保存状态并在app启动时进入合适状态，还是很容易写出来的。

本以为主屏幕启动的webapp运行环境和safari浏览器并无多大差异，事实也确实差异不大，但那差异的部分却几乎“ 要了webapp的命 ”。除了上篇文章提到的一个明显问题，这里再讨论些暗问题。（以下问题在ios4.3上做过测试）  
1、**启动、运行慢**   
各种测试表明：主屏幕启动的webapp运行速度比在浏览器里运行，慢2到2.5倍。也许ios5、6会有改观，但也很难会有大的进步，至少每次切换程序回来就得重新刷新页面，并且不走缓存，而是重新请求资源，只这点就严重拖累了速度。  
2、**两个不同js引擎**  
"Essentially, there are two different JavaScript engines," says Alex Kessinger, "They're not using the new JavaScript engine with applications that launch from the home screen." 有待验证。  
3、**不能用浏览器基本缓存系统以及HTML5 Application Cache**  
也就是说他们不能运行离线应用。同样也许ios5、6上修复了不能用离线存储的问题，但能否运行的好仍很难确定，待验证。  
4、**渲染时不能用最新的‘异步模式’，只能用‘同步模式’**  
很明显异步模式渲染，看起来也许更流畅。这里渲染模式似乎无法测试得出究竟，但提出这个问题，想必并非胡编乱造，可查找更多证据佐证。  
5、**页面内有跳转，需要阻止a标签的默认行为**  
对于webapp单页应用，本来就不该存在页面跳转，但也确实有合理的跳转需求，例如多个webapp关联起来（可能是不同的域，但互相有按钮链接，例如返回按钮），如果简单的用个a标签指向另一个app，那么在从主屏幕打开的app会跳转到浏览器里打开页面，而不是直接切换页面，这个如何解决呢？办法如下：  

```js
$('a').click(function() {
  document.location = $(this).attr('href');
  return false;
});
```

即把页面中的a标签链接默认行为禁止掉，转而走js的跳转。这个对于链接中含有参数的情形也有效。但是，有没觉得这个解决办法并不太好？另外如果链接指向的是不受控制的外部页面，用这个办法跳转过去，就无法再跳转回来了，因为没有返回按钮，没有导航栏按钮。

这些问题也会出在用UIWebView API的本地程序中，即Hyrbid App中，不过在这类混合型app中，也许可以用native的特性弥补这方面的不足。但是，在webapp如日中天的今日，没有了native的帮助，怎么能解决？  

- 其实最彻底的解决，即是**规避**，主屏幕图标点开，也走浏览器。即去掉head标签里的`<meta content="yes" name="apple-mobile-web-app-capable" />`这样点图标就直接打开了浏览器。但是也许那些把网站添加到主屏幕的用户会抱怨说，怎么能走浏览器呢，怎么不是全屏了，怎么又有讨厌的导航栏了，这不是和native app不一样了...  
- 除此之外，解决办法还是离不开web storage。也许Application Cache不能用，但localStorage应该没问题，大数据也可以探索下Web SQL / IndexedDB等，safari收藏夹里的《iphone使用手册》即用了很多存储技术。

这些问题使得webapp与native app之间的差距更大，本来webapp性能就不如native，因为这些问题，连做个表面看起来类似native的webapp都几乎成为泡影，除非你对此视而不见、或想办法解决它。现在有一些人鼓吹html5页游，试想因为这些问题，页游怎么能运行在ios上？不过也确实很少见到运行在ios上的页游。这些问题对于展示型业务（像淘宝的大部分业务）来说可能是小问题，不足以重视，但对于游戏以及媒体网站业务来说，感觉真的不容忽视。

主屏幕webapp问题，至此感觉已探究的差不多，但最终也只是抛出了问题，而本人一点儿没解决掉，遗憾的是也没搜索到其他某某对此的解决方案。也许这个确实是个很难解决的问题，也许这ios的bugs本该就等待苹果的人去解决，也许看到文章的你能写出实际的解决方案。

### 引用 
- [http://www.theregister.co.uk/2011/03/15/apple_ios_throttles_web_apps_on_home_screen/](http://www.theregister.co.uk/2011/03/15/apple_ios_throttles_web_apps_on_home_screen/)  
- [http://stackoverflow.com/questions/8476662/webapp-for-iphone-switches-from-home-screen-to-safari-when-url-parameters-are-us](http://stackoverflow.com/questions/8476662/webapp-for-iphone-switches-from-home-screen-to-safari-when-url-parameters-are-us)
