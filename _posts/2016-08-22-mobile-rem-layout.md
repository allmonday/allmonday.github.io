---
layout: post
title:  "移动端布局小结"
categories: jekyll update
tags: mobile rem
---


# rem 布局准备
选择rem 还是出于兼容性的考虑, 使用的方案是动态修改html 的font-size.
p.s.: 以下代码都是基于640px像素的设计稿

```javascript
	function initSize() {
		var deviceWidth = document.documentElement.clientWidth;
		if(deviceWidth > 640) deviceWidth = 640;
		document.documentElement.style.fontSize = deviceWidth / 6.4 + 'px';
	}
	initSize();
	document.querySelector('.landing-container').className = ".landing-container";  // delete hide class
	jQuery(function () {
		$(window).resize(initSize);
	})
```

注意代码执行之前, 页面会有个变化过程, 所以最好在初始化的时候隐藏html元素.

样式方面需要注意, 将body 的宽度设置为浏览器像素同样宽度

```css
body {
	width: 6.4rem;
	margin: 0 auto;
}
```

当然viewport也必须设置好

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=0, minimum-scale=1.0, maximum-scale=1.0">
```

# 字体
字体可以设置几个断点, 使用不同的字体大小, 我把font size都用class 的形式写了出来, 然后套用媒体查询根据断点使用不同的字体大小

```sass
@media screen and (max-width: 320px) {
	@for $fontSize from 10 through 25 {
		.font-#{$fontSize} {
			font-size: #{$fontSize}px;
		}
	}
}
	
@each $screenWidth in (320, 375, 414, 539) {
	@media screen and (min-width: #{$screenWidth}px) {


	@for $fontSize from 10 through 25 {
		.font-#{$fontSize} {
			font-size: #{ round( $fontSize * ($screenWidth / 320))}px;
		}
	}

	}
}

```

# 具体布局

640px 宽度的时候, font-size的值是100px, 元素尺寸需要根据设计稿给出对应的rem, sass中添加一个px转换rem 的函数:

```sass
@function pxRem($px) {
	$baseFontSize: 100px;
	@return $px / $baseFontSize * 1rem;
}
```

然后代码中就可以直接根据设计稿填写尺寸

```sass

.landing-footer {
	color: #ccc;
	margin-top: pxRem(70px);
	height: pxRem(82px);
	line-height: pxRem(82px);
}

```


# 结束

这样就能基本实现mobile端的等比例缩放的布局了, 可以保证在不同浏览器尺寸下有相同的布局体验,
当然宽度的上限设置为了640px, 超过这个尺寸就该去pc端页面了.

另外在手机浏览器中,flex布局实现居中是非常方便的,而且兼容性也不错,可以多用用.
