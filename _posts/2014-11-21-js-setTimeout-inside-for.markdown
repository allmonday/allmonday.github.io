---
layout: post
title:  "setTimeout inside for loop"
categories: jekyll update
tags: javascript async
---

## for loop 中的异步操作

先上一个代码

```javascript
for( var i = 0; i < 10 ; i++) {
	setTimeout(function () {
		console.log(i);
	}, 0);
}
```

会输出什么？是 1,2,3,4 .. 吗？ 输出的是 10, 10, 10, 10 ..

[stackoverflow 上的回答](http://stackoverflow.com/questions/5226285/settimeout-in-a-for-loop-and-pass-i-as-value)


```
If you don't do something like this (and there are other variations on this same idea), then each of the timer handler functions will share the same variable "i". When the loop is finished, what's the value of "i"? It's 3! By using an intermediating function, a copy of the value of the variable is made. Since the timeout handler is created in the context of that copy, it has its own private "i" to use.
---------
(大意) 每个timeout 的 handler会共享同一个i, 当循环结束的时候, i的值是3。
使用一个闭包保存 i 的当前值, timeout 就会使用这个闭包中的 i。
```

方法有以下几种 


```javascript
for( var i = 0; i < 10 ; i++) {
	(function (i) {
		setTimeout(function () {
			console.log(i);
		}, 0);
	})(i);
}
```

```javascript
var a = [1,2,3,4,5,6,7,8,9]

a.map(function (i) 
	setTimeout(function () {
		console.log(i);
	}, 0)
});

```

```javascript
var async = require('async');

async.times( 10, function (i) {
	setTimeout(function () {
		console.log(i);
	})
})

```