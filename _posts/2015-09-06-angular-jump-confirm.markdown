---
layout: post
title:  "[augnular] 页面跳转确认"
categories: front-end
tags: ui.router angular
---

经常会遇到, 填写到一半的表单, 用户点击了其他的链接, 需要跳出对话框确认是否放弃内容.
在`angular`里, 借助`ui.router`这个功能实现起来相当方便.

```javascript
angular.module('tutorialApp')
  .controller('JumpCtrl', function ($scope, $q) {
    $scope.$on('$stateChangeStart', function (event, toS, toP, fromS, fromP, err) {
      var result = confirm('you want to leave');
      if (!result) { // 如果不想走了, 那就取消跳转
        event.preventDefault();
      }
    });
  });
```

1. `$stateChangeStart`是 `ui.router`定义的一个事件, 在跳转发生之前触发
2. `event.preventDefault()` 阻止了页面跳转的事件(此处的event 是 angular 包装过的事件对象).

注:

`confirm`对话框可以换成 `angular.ui` 中的 `modal`, 也非常的方便.
