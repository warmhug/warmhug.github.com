---
layout: post
title: "文件上传前的压缩处理方法讨论"
tags: [js]
author_name: warmhug
---

最近一个项目中涉及到上传文件的操作，由于用户网速差异以及上传文件的大小等原因导致一些上传失败的案例。随即想到浏览器端可以先对要上传的文件进行压缩，然后上传其压缩版本到服务器，服务器端对其进行相应的解压缩即可。这样就解决了诸多上传过程中引起的问题。

不过，很明显现在的浏览器是不支持这样的想法的，那么先看看其他高人对此有何妙招吧，google一把发现了好多东西。09年就有人在stack overflow上提出此问题，回答者说用flash、sliverlight之类的浏览器插件能实现。随着html5标准的推行以及浏览器的支持，现在已经有另外几种方法支持此功能的实现，但还是避免不了兼容性问题。

这里讨论一些已经实现的方法：  
1、flash插件：（由于不懂flash，此处只列出相关资料地址）  
[http://livedocs.adobe.com/flex/3/html/help.html?content=17_Networking_and_communications_7.html](http://livedocs.adobe.com/flex/3/html/help.html?content=17_Networking_and_communications_7.html)  
[http://code.google.com/p/ascompress/](http://code.google.com/p/ascompress/)  
sliverlight实现方法自行查找。  
2、对于上传图片：  
**原理**：加载一个图片到canvas元素里，改变其尺寸，然后从canvas中取出改变后的图片数据，上传。  
这里（[http://stackoverflow.com/questions/923885/capture-html-canvas-as-gif-jpg-png-pdf](http://stackoverflow.com/questions/923885/capture-html-canvas-as-gif-jpg-png-pdf)）有不少人参与的实现方法讨论，但貌似最终没有个很好的方法。   
另外有一个实现好的方案：[http://makeitsolutions.com/labs/jic/](http://makeitsolutions.com/labs/jic/)。这个方案看起来挺不错，看代码用到了canvas和file api，浏览器兼容性应该会有问题，但的确是个简洁易行的方案。  
3、**一个很牛的框架解决方案--Plupload**：  
地址：[http://www.plupload.com/index.php](http://www.plupload.com/index.php)  
使用代码示例：  

```js
$("#uploader").pluploadQueue({
    // General settings
    runtimes : 'gears,flash,silverlight,browserplus,html5',
    url : 'upload.php',
    max_file_size : '10mb',
    chunk_size : '1mb',
    unique_names : true,

    // Resize images on clientside if we can
    resize : {width : 320, height : 240, quality : 90},

    // Specify what files to browse for
    filters : [
        {title : "Image files", extensions : "jpg,gif,png"},
        {title : "Zip files", extensions : "zip"}
    ],

    // Flash settings
    flash_swf_url : '/plupload/js/plupload.flash.swf',

    // Silverlight settings
    silverlight_xap_url : '/plupload/js/plupload.silverlight.xap'
});
```

支持广泛性很明显：gears,flash,silverlight,browserplus,html5  
亮点配置功能：  
**chunk_size** -- 把文件拆成更小的块数据，例如后端要求上传文件大小为1M，你可以把10M的文件拆分为10个上传请求，完成上传。  
**headers** -- 允许向HTTP requests增加自己的键值。  
**resize** -- 如果可以，会改变客户端图片的尺寸和质量，然后再上传。  
这些都是特别实用的功能，其他api大家自行了解。

讨论中有说到用js的FileSystem API（[http://slides.html5rocks.com/#filewriter](http://slides.html5rocks.com/#filewriter)），可能把图片数据读取到js中，再重新处理图片数据，然后上传，但这种方式似乎还没人有实现。

还有一个更为彻底和有新意的思路，其中已经有实现方案：  
**思路：压缩允许格式（jpeg、mp3等）的文件以及文件夹，合并生成一个.zip文件，然后上传。**  
案例：  
JFileUpload -- [http://www.jfileupload.com/products/jfileupload/documentation/tutorials/compression.html](http://www.jfileupload.com/products/jfileupload/documentation/tutorials/compression.html)   
JSZip -- [http://stuk.github.com/jszip//](http://stuk.github.com/jszip//)  
其中JSZip还可以对压缩好的zip文件进行添加、删除等编辑操作。

文件上传操作，在web日益发展的今天已经显得越来越频繁，越来越重要！对于mobile端，安卓、ios6等都已经支持在网页上选择本地图像上传到网站；mobile端的网速相对于同样网络的pc端稍慢，而且大多是用手机网络，对于流量自然是能省则省，将文件压缩然后再上传的优势就很明显。以上的所有解决方案虽然能解决大部分问题，但最终都不是最好的办法，最好的当然是浏览器本身对此的支持。期望这一天能尽早到来。

### 链接
1. [http://webmasters.stackexchange.com/questions/28445/upload-image-file-is-compression-on-client-side-already-possible](http://webmasters.stackexchange.com/questions/28445/upload-image-file-is-compression-on-client-side-already-possible) 
2. [http://stackoverflow.com/questions/8377268/file-compression-before-upload-on-the-client-side](http://stackoverflow.com/questions/8377268/file-compression-before-upload-on-the-client-side)
