---
layout: post
title:  "使用browsersync 和 gulp优化开发体验"
categories: jekyll update
tags: browsersync gulp proxy
---

## 场景说明:

对于一个传统的网站开发流程, 一般都是这样的:

* 前端切图, 制作页面, 样式
* 完成之后, 交付给后端, 后端套模板
* 联调时, 一般会出现些样式问题, 如果前端使用的是sass等工具, 需要手动拷贝输出的样式文件
* 来回若干次之后完成调试


在页面的生存周期中, 有两个环节, 一个是前端做静态页面, 一个是页面套模板到后端之后的后续迭代.

痛苦往往发生在后续迭代中, 会遇到模板内容修改和样式修改等需求, 对于前端来说会有两种选择, 一是在自己的前端项目中修改页面, 修改样式, 修改完页面之后, 把差异的部分修改到后端模板之中, 把css也更新过去.

另一种是直接修改后端项目中的模板和样式文件, 相当于废弃了前端自己的项目.

第一种方法的好处是可以享受到前端构建工具的便利, 比如sass, autoprefixer, jslint, babel等, 但是需要维护前端项目中页面和后端模板的一致, 不然后续迭代会很困难

第二种方法的好处是修改比较直接, 但是没有了构建工具的种种便利, 开发效率会有下降.


## gulp & browsersync

通过上面的比较, 可以发现方案一中, 痛点其实在于维护页面和模板的差异上, js和css可以通过加上gulp.task 轻松的输出到后端项目(或者静态服务器)上.

而对于页面和模板的差异, 我们需要在两者之中做一个取舍.
取模板, 舍弃原始的页面.

假设我们现在运行了后端项目, 去到了要去编辑的页面, 然后开始编辑页面, 根据所用框架的不同, 有些可能会刷新页面, 有些可能需要手动修改,这里如果会自动刷新可以带来开发效率的提升.而静态资源依旧使用前端项目(es6, sass 依旧发挥着作用), 但在编辑样式的时候, 我不希望需要频繁的刷新页面, 因为后端项目页面状态是有关联关系的, 如果刷新了可能还需要一些操作才能找到要修改样式的目标.
 
在前端开发的时候我们会经常用到browsersync, 它处理css文件的时候会自动替换css文件, 而不用重新刷新页面. 所以browsersync处理样式修改非常方便.

所以思路渐渐清晰起来了, 利用gulp和browsersync, 把后端模板和样式的编辑自动化起来.

## 方案:

### 1.监控模板变化

development.json

```json
{
	"concat": false,
	"minifiy": false,
	"strict": false,
	"cachebust": false,
	"breakOnError": false,
	"langType": "en",
	"sync": true,
	"proxy": false,
	"proxy_port": 8080,

	"proxy_tornado": true,
	"proxy_tornado_url": "localhost:9000",
	"proxy_static_address": "/Users/tangmin/Documents/friendbuy/friendbuy/static",
	"proxy_templates": "/Users/tangmin/Documents/friendbuy/friendbuy/template/**/*.html",
	
	"server": {
		"port": "9001"
	}
}
```

gulpfile.js

```javascript

gulp.task('reload_template', function () {
	return gulp.src([config.proxy_templates])
		.pipe(tool.reload());
})

gulp.task('watch', function () {

	if (config.proxy_tornado) {
		// 监控模板修改
		gulp.watch(config.proxy_templates, ['reload_template']);
	}
	var scriptWatcher = gulp.watch('src/**/*.{js,json}', ['script']);
	var styleWatcher = gulp.watch('src/**/*.{css,scss}', ['style']);
	gulp.watch('src/**/*.{jpg,png}', ['image']);
	gulp.watch('src/**/*.html', ['html']);
})
```

### 2.监控静态文件变化, 并同步文件

style.js

```javascript
module.exports = function (gulp, $, config, tool) {
	var src = "src/**/*.{css,scss}";
	var dest = "public";

	return function () {
		return gulp.src(src)
			.pipe($.plumber())
			.pipe($.sass().on('error', $.sass.logError))
			.pipe($.autoprefixer({
				browsers: ["> 1%"],
			}))
			.pipe(gulp.dest(dest))
			.pipe(gulp.dest(config.proxy_static_address))  // 多输出一份到后端目录下
			.pipe(tool.reload());
	}
}
```

js 的也是同样的操作

### 3.browsersync 代理页面刷新后端页面

然后就是browsersync proxy好用的地方了, 代理后端项目页面的刷新:

```javascript
browserSync.init({
	open: true,
	proxy: config.proxy_tornado_url,  // "localhost:9000",
})

```

然后打开代理的(eg: 3000) localhost:3000, 编辑模板的时候页面会自动刷新, 而编辑样式文件的时候页面会自动重载样式, 不会把整个页面都刷新一遍. 
建议的迭代编辑流程是:
1. 先把模板的元素全部写好
2. 再编辑样式 (避免重复操作, 比如那些要展开才能看到的元素)
3. 再修改js(如果有需要的话)


通过这样的组合, 模板和静态的修改既保留了构建工具的高效, 又避免了模板和页面出现分歧的窘境.





