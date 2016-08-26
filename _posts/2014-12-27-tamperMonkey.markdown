---
layout: post
title:  "使用tamperMonkey记录[生活]"
categories: jekyll update
tags: jquery tampermonkey
---

## tampermonkey 

它是款chrome 插件，可以执行用户定义的js脚本， 它有什么用呢？ 设想一下一些场景：

- 喜欢看的漫画网站广告太多了，想去掉
- 网站的布局我不喜欢，想拉宽点
- 等待30秒后自动跳转？ 自动点
- sb网站header太大，扎眼，hide

tampermonkey就能轻松完成这些任务， 我们一起来试试吧:

## 安装

[https://chrome.google.com/webstore/detail/tampermonkey/dhdgffkkebhmkfjojejmpbldmpobfkfo?hl=zh-CN](https://chrome.google.com/webstore/detail/tampermonkey/dhdgffkkebhmkfjojejmpbldmpobfkfo?hl=zh-CN)

装好之后右上角就多了一个猴子屁股，点击仪表盘，在`已安装脚本`左边有一个新建按钮

会发现一些预定义的代码, 稍作修改（以隐藏header为例)

```javascript
// ==UserScript==
// @name         header hider
// @namespace    http://www.qisense.com/
// @version      0.1
// @description  clean header part
// @author       kikodo
// @match        http://www.qisense.com/*
// @grant        none
// ==/UserScript==
```

内容很简吧，`@match`定义了这个脚步的运行网站，之后符合该pattern的站点才会执行这个脚本。

那我们写一段试试吧

## 使用

```javascript
$(function () {
	$('header').hide();
})
```

保存完之后在刷新一下`qisense`, 就会发现header被藏掉了。恩， 就是这么简单。

## 小实验： 将浏览页面href 发送到我的服务上

这个例子太简单了，没意思，我想要一个功能，把我每次访问的页面地址收集起来，然后做些分析看看，看看每个时间段都会看些什么网站。

服务器假设在localhost:8888上吧， 之后会放到自己的linode上。

试了一下`$.ajax` ,对跨域不是很熟悉， 试了下没成功。 然后想到`html`的 `form`可以跨域post，那就用jquery拼一个form，把信息放进去传送吧。

代码如下:

```javascript
// ==UserScript==
// @name         action collector
// @namespace    http://your.homepage/
// @version      0.1
// @description  enter something useful
// @author       You
// @match        *://*/*
// @grant        none
// ==/UserScript==
$(function () {    
    var $form = $('<form method="POST" action="http://localhost:8888"><form>')
    $form.append('<input name="name" value="' + window.location.href +'" type="text">')
    $form.append('<input type="submit" value="Submit">')
    $form.submit()
})
```

运行试试，恩，没问题能收到， 但我当前的页面跳转到`localhost:8888`了， 这可不行啊，我还要看的啊！！

所以要给页面添加一个`iframe`，放在`body`的最下面，设行高为0不可见（或者 hidden)

然后把`form`的 `target` 设为`iframe`, 修改代码如下:

```
$(function () {    
    $('body').append('<iframe name="xiframex" style="height: 0px;"></iframe>')
    var $form = $('<form target="xiframex" method="POST" action="http://localhost:8889"><form>')  /* target */
    $form.append('<input name="name" value="' + window.location.href +'" type="text">')
    $form.append('<input type="submit" value="Submit">')
    $form.submit()
})
```

这样一来页面就不会跑走了，爽。

用了几次发现一个页面如果有多个`iframe`， 每个都会执行一次脚本，这个可不好啊， tampermonkey想到了这个情况，只需要添加@noframes 在头上就行了

```javascript
// @name         action collector
// @namespace    http://your.homepage/
// @version      0.1
// @description  enter something useful
// @author       You
// @match        *://*/*
// @grant        none
// @noframes
```

顺带一条， `*://*/* `这样的写法就能保证所有页面都会运行了。

最后放一个服务器的的 tornado 代码, 基本上什么都没写~啊哈哈，不要在意细节。

```python
import tornado.ioloop
import tornado.web

class MainHandler(tornado.web.RequestHandler):

	def post(self):
		print('-->post')
		name = self.get_argument('name', None)
		print(name)

application = tornado.web.Application([
	(r"/", MainHandler),
])

if __name__ == "__main__":
	application.listen(8888)
	tornado.ioloop.IOLoop.instance().start()
```

最后贴一下输出 

```
-->post
http://shu.taobao.com/
-->post
http://www.7330.com/op/#liebiao
-->post
http://www.7330.com/op/1108418.shtml
-->post
http://www.7330.com/op/1108774.shtml
-->post
http://www.7330.com/op/1108774_2.shtml
```

## 更新
TamperMonkey 有支持跨域的ajax 方法, 脚本简化如下:

```javascript
// ==UserScript==
// @name         ajax sender
// @namespace    http://tampermonkey.net/
// @version      0.1
// @description  send ajax
// @author       You
// @match        *://*/*
// @exclude      http://115.com/*
// @exclude      http://weixin.tangkikodo.com/*
// @grant        GM_xmlhttpRequest
// @require      http://cdn.staticfile.org/jquery/3.0.0-alpha1/jquery.min.js

// ==/UserScript==
/* jshint -W097 */
'use strict';

$(function () {
    console.log('start record');
    var url = window.location.href;
    var title = $('title').text() || "";

    GM_xmlhttpRequest({
        method: "POST",
        url: "http://git.tangkikodo.com",
        data: "name="+ url + "&title=" + title,
        headers: {
            "Content-Type": "application/x-www-form-urlencoded"
        },
        onload: function(response) {
            console.log('record end');
            console.log(response);
        }
    });
});


```

## 其他问题

1. https 的链接无法发送 

>Refused to frame 'http://localhost:8888/' because it violates the following Content Security Policy directive: "frame-src 'self' render.githubusercontent.com gist.github.com www.youtube.com player.vimeo.com checkout.paypal.com".

2. 还是得用`jsonp`这个方法更主流一些吧

## 参考链接

- [tampermonkey doc](http://tampermonkey.net/documentation.php#_grant)
- [tornado doc](http://www.tornadoweb.cn/documentation#_7)
- [cooper 的 scripts](https://github.com/CooperLuan/tmonkey-script)
