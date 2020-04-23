---
title: chromeExtend
date: 2018-10-15 14:12:52
tags:
---


### 简介

从功能上讲：Chrome扩展主要用于对浏览器功能的增强。它可以捕捉特定网页的内容,（比如，移除网页弹窗广告dom，实现屏蔽广告），捕捉HTTP报文，捕捉用户浏览动作，改变浏览器地址栏/起始页/书签/Tab等界面元素的行为，与别的站点通信，更改浏览器代理服务器的设置等等。

从表现形式上讲：Chrome扩展就是个后台运行的网页而已,所以其开发使用的是web技术，开发插件我们实际上就是写一个Web应用，然后将按照Chrome的规定将一个快捷方式放在Chrome工具栏上。



#### 为什么是chrome扩展
+ Chrome占有率更高，更多人用；
+ 开发更简单；总体上看没什么新的技术，开发语言就是javascript，Firefox的插件开发则复杂许多，涉及到环境的搭建和一些WEB以外的技术；IE的插件开发就更复杂了，需要熟悉C++和COM技术，当然还要装微软的Visual Studio。
+ 应用场景更广泛，Firefox插件只能运行在Firefox上，而Chrome除 了Chrome浏览器之外，还可以运行在所有webkit内核的国产浏览器，等；
除此之外，Firefox浏览器也对Chrome插件的运行提供了一定的支持；
<!--more-->


### Manifest文件格式

manifest.json 是 Chrome 扩展的入口文件，定义了 扩展的名称（name）、版本（version）、描述（description）、图标位置（icons）和 Manifest 版本（manifest_version）等信息。

其中，name、version 和 manifest_version 是必须的，而且 manifest_version 必须为 2 。

下面是是一个manifest.json示例

 ```
  {
   
   // app属性是chrome应用所必须的      
   // chrome扩展与应用的区别下面会讲到
    "app": { 
        "background": {
            "scripts": ["background.js"]
        }
    },
      // 清单文件的版本，这个必须写，而且必须是2
    "manifest_version": 2,
    "name": "My Extension",
    "version": "versionString",
    "default_locale": "en", //扩展的语言
    "description": "A plain text description",
      // 此处的图标可能会被用于多处，右键菜单，扩展提醒等
      // 可以只设置一种尺寸的图标比如 19x19
    "icons": { 
        "16": "images/icon16.png",
        "48": "images/icon48.png",
        "128": "images/icon128.png"
    },
      // 浏览器右上角图标设置，
    "browser_action": {
        "default_icon": {
            "19": "images/icon19.png",
            "38": "images/icon38.png"
        },
        "default_title": "Extension Title",
        "default_popup": "popup.html"
    },
      // 当某些特定页面打开才显示的图标
    "page_action": {
        "default_icon": {
            "19": "images/icon19.png",
            "38": "images/icon38.png"
        },
        "default_title": "Extension Title",
        "default_popup": "popup.html"
    },
    "background": {
        "scripts": ["background.js"]
    },
   // content_scripts属性可以指定将哪些脚本何时注入到哪些页面中，当用户访问这些页面后，相应脚本即可自动运行，从而对页面DOM进行操作。
    "content_scripts": [
        {
            "matches": ["http://www.google.com/*"], //    定义了哪些页面会被注入脚本
            "exclude_matches":[""],
            "all_frames":[""],
            "exclude_globs":[""],
            "include_globs":[""],
            "css": ["mystyles.css"],
            
            "js": ["jquery.js", "myscript.js"],
          
        }
    ],
     // 插件配置页页面
    "options_page": "options.html",
         // 覆盖浏览器默认页面
    "chrome_url_overrides":
    {
        // 覆盖浏览器默认的新标签页
        "newtab": "newtab.html"
    },
    "permissions": [
        "contextMenus", // 右键菜单
        "tabs", // 标签
        "notifications", // 通知
        "webRequest", // web请求
        "webRequestBlocking",
        "storage", // 插件本地存储
        "http://*/*", // 可以访问的网站
        "https://*/*" // 可以访问的网站
    ],
     // 向地址栏注册一个关键字以提供搜索建议，只能设置一个关键字
    "omnibox": { "keyword" : "go" },
    "web_accessible_resources": [
        "images/*.png"
    ]
}
 
 
 ```
 

 
 

 
 
### content_scripts
 
 content_scripts是在网页上下文中运行的文件。通过使用标准文档对象模型( DOM )，他们能够阅读浏览器访问的网页的细节，对其进行更改。
 

 
 关于匹配规则：
 >其中matches属性定义了哪些页面会被注入脚本，exclude_matches则定义了哪些页面不会被注入脚本，css和js对应要注入的样式表和JavaScript，run_at定义了何时进行注入，all_frames定义脚本是否会注入到嵌入式框架中，include_globs和exclude_globs则是全局URL匹配，最终脚本是否会被注入由matches、exclude_matches、include_globs和exclude_globs的值共同决定。简单的说，如果URL匹配mathces值的同时也匹配include_globs的值，会被注入；如果URL匹配exclude_matches的值或者匹配exclude_globs的值，则不会被注入。
 
### backgroundjs
 
```
 "name": "我的扩展程序",
  ...
  "background": {
    "scripts": ["eventPage.js"],
    "persistent": false
  },
  ...

```
background分两种：常驻后台页面与事件后台页面。如果persistent设置为true(默认就是true)则是常驻后台页。

长驻后台浏览器打开即运行，并且一直运行直到浏览器关闭。

事件页面只在需要时加载，当事件页面不活动时就会卸载，以便释放内存和其他系统资源。

事件页面在“需要”时加载，再次进入空闲状态后卸载。如下是一些可能使事件页面加载的例子：

 + 应用或扩展程序第一次安装或者更新到新版本（为了注册事件）。
 + 事件页面监听的某个事件触发。
 + 内容脚本或其他扩展程序发送消息。
 + 扩展程序中的其他视图调用了 runtime.getBackgroundPage。

 
### 跨域请求
 
 跨域指的是JavaScript通过XMLHttpRequest请求数据时，调用JavaScript的页面所在的域和被请求页面的域不一致。对于网站来说，浏览器出于安全考虑是不允许跨域。
 
 Google允许Chrome扩展应用不必受限于跨域限制。但出于安全考虑，需要在Manifest的permissions属性中声明需要跨域的权限。

比如，如果我们想设计一款获取维基百科数据并显示在其他网页中的扩展，就要在Manifest中进行如下声明：

```
{
    ...
    "permissions": [
        "*://*.wikipedia.org/*"
    ]
}
```


### 带选项页面的扩展


mainfest 清单如果配置了options_page或options_ui属性，扩展会有一个‘选项’菜单。点击选项会打开配置的的html页面。此功能多用于扩展设置。

options_page是在新的tab标签页打开配置页面，options_ui是弹出层形式显示配置页面


### 扩展页面间的通信

Chrome提供了4个有关扩展页面间相互通信的接口，分别是runtime.sendMessage、runtime.onMessage、runtime.connect和runtime.onConnect。

Chrome提供的大部分API是不支持在content_scripts中运行的，但runtime.sendMessage和runtime.onMessage可以在content_scripts中运行，所以扩展的其他页面也可以同content_scripts相互通信。

runtime.sendMessage完整的方法为：
```
chrome.runtime.sendMessage(extensionId, message, options, callback)
```
extensionId用于backgroundjs识别来自哪个扩展的标示，可以传null标示用扩展的默认id(一串没有规律的字符串)

message是所要发送的消息体，必须是可以JSON序列化的值。

callback回调方法，backgroundjs成功接收到消息时可以调用此方法。



runtime.onMessage完整的方法为：

```
chrome.runtime.onMessage.addListener(callback)
```

callback方法有三个参数：message,senderId,callbackFn

message收到的消息体，

senderId消息发送方的id，

callbackFn消息发送方的回调函数





### Browser Actions

##### defautlt_icon

Browser Actions设置default_icon属性指定扩展的默认图片。如果不设定将使用chrome默认的图标。也可以通过setIcon方法可以动态更改扩展的图标，setIcon的完整方法如下：
```
chrome.browserAction.setIcon(details, callback)
```
##### popup页面
popup属性指定点击扩展图标时打开的页面
##### default_title
将鼠标移至扩展图标上，片刻后所显示的文字就是扩展的标题。
##### Badg
badg是扩展图标上面的提示信息，最多支持4个字节。它的优势就是可以一直显示。比如记录邮件、微博、RSS阅读器等的未读条目。Badge目前只能够通过JavaScript设定显示的内容

下面的代码显示了一个背景颜色为蓝色，内容为“Dog”的badge：

![avatar](/assets/blogImg/bdge.jpg)

```
chrome.browserAction.setBadgeBackgroundColor({color: '#0000FF'});
chrome.browserAction.setBadgeText({text: 'Dog'});
```


### 右键菜单
在Manifest的permissions域中声明contextMenus可以将扩展添加至右键菜单。右键菜单分为四类，分别为普通菜单，单选，复选，和分割线。其中普通菜单可以有子级菜单

创建菜单使用create方法。

```
chrome.contextMenus.create({
    type: 'normal',
    title: 'Menu A',
    id: 'a',
    contexts: ['link']
});
```

其中contexts定义何时显示自定义的菜单项，上例是在超链接上右键时显示自定义的菜单。contexts完整的选项包括all、page、frame、selection、link、editable、image、video、audio和launcher，

如果在创建菜单时，定义了onclick域，则菜单被点击后就会调用onclick指定的函数。调用的函数会接收到两个参数，分别是点击后的相关信息和当前标签信息。点击后的相关信息包括菜单id、上级菜单id、媒体类型（image、video或audio）、超级链接目标、媒体URL、页面URL、框架URL、选择的文字、是否可编辑（只针对text input和textarea等控件）、用户点击前是否被选中和当前是否被选中（只针对checkbox或radio）


右键菜单典型例子：

<div align=center>
<img src="/assets/blogImg/contextMenus.png" width = "300"  />
</div>

```
 "permissions":[
        "contextMenus",
        "tabs"
        ]

chrome.contextMenus.create({
    type: 'normal',
    title: '使用谷歌搜索：%s', // %s表示选中的文字
    id: 'a',
    contexts: ['selection'],
    onclick: function(params)
    {
       
        chrome.tabs.create({url: 'https://www.google.com/search?q=' + encodeURI(params.selectionText)+'oq='+ encodeURI(params.selectionText)});
    }
});

```


### 桌面提醒

要使用桌面提醒功能，需要在Manifest中声明notifications权限

<div>
<img src="/assets/blogImg/desktopNotify.png"  width="400" />
<div>

```
"permissions": [
    "notifications"
]

```


创建桌面提醒

```
chrome.notifications.create('', {
        "type":"basic",// basic,image,list,progress
        "iconUrl":"/images/icon48.png",
        "title":"Tips",
        "message":"休息片刻继续工作，效率会更高"
    }, function(){
    
    })
```
###多功能框omnibox

Chrome和其他浏览器相比一个最大的区别就是地址栏——其实不仅仅是地址栏，而是一个多功能的输入框，Google将其称为omnibox（中文为“多功能框”）。我们熟悉的一个功能就是用户可以直接在omnibox搜索关键字，Chrome也将omnibox开放给开发者，这使得omnibox更加强大。

要使用omnibox需要在Manifest的omnibox域指定keyword：

```
"omnibox": { "keyword" : "hamster" }
```

Omnibox有四种事件：onInputStarted、onInputChanged、onInputEntered和onInputCancelled，分别用于监听用户开始输入、输入变化、执行指令和取消输入行为。其中执行指令是指用户敲击回车键或用鼠标点击建议结果。

onInputChanged接受一个回调方法做为参数，这个回调函数又有两个参数，第一个参数是字符串型，值为用户当前的输入值，第二个参数还是function型，用于返回建议结果，建议的结果为数组型数据，数组中的元素是建议结果对象。


<img src="/assets/blogImg/omnix.png" width="400" />

```
chrome.omnibox.onInputStarted.addListener(function () {

})


chrome.omnibox.onInputChanged.addListener(function (text, suggest) {
    console.log(text)
    suggest([{
        "content": "js",
        "description": 'input 1 search js'
    },
    {
        "content": "css",
        "description": 'input 2 search css'
    }
    ])
})

chrome.omnibox.onInputEntered.addListener(
    function (text) {
        var newURL = 'https://www.google.com/search?q=' + encodeURIComponent(text);
        if (text == 1) {
            var newURL = 'https://www.google.com/search?q=js';
        } else if (text == 2) {
            var newURL = 'https://www.google.com/search?q=css';
        }
        chrome.tabs.create({ url: newURL });
    });


```


这里有一点要注意：要用严格模式。最开始我写的'content'（单引号）造成看不到预期的效果，并且也没有任何报错。调试好久才找到问题。



### 参考资料

<div>
    <a href="https://developer.chrome.com/extensions">官方文档</a>
</div>

<div>
    <a href="http://www.ituring.com.cn/book/miniarticle/110853">chrome扩展及应用开发</a>
</div>

<div>
 <a href="https://crxdoc-zh.appspot.com/extensions/content_scripts">https://crxdoc-zh.appspot.com/extensions/content_scripts</a>
</div>

<div style="margin-bottom:30px;"> </div>








