---
layout: post
title:  "angular $http 中的 transformResponse & interceptor"
categories: angular
tags: javascript ajax $http
---

最近工作项目中定义了一下`ajax`数据的状态规范, 发现和 angular 结合的时候还挺蛋疼的.
其中主要头疼的问题是 transformResponse 和 interceptor 的执行顺序问题:

1. 在interceptor 中我们期望可以根据返回json中的 status 字段来resolve 或 reject一个 response.
2. 在 transformResponse 里面, 我们可以在回调接收到数据前, 对数据内容进行处理. 例如$resource 中使用transformResponse 将restful 的对象从 data 字段中取出来.
3. 问题来了: 在 angular 中, transformResponse 的执行顺序是先于 interceptor中的, 假设我们在某个 $resource 的 action 中使用 transformResponse 将数据 `return data.data` 了, 那么在稍后传入到 interceptor (response) 中的 response 对象里的 data 字段就变成了原先的 data.data (因此, message 和 status 这些字段都没了), 此时, interceptor 的心情就会和开发者的心情一样复杂...

以下是一个不算完的方案, 在 data内中**不**同时含有 `data, status, message` 的情况下可以正常使用.



### http status: 200
```json  
{
    'message': 'good job',
    'data': 'return data'
    'status': 0
}
```

备注:

对于restful的接口, 需要将原来直接返回的数据包在data字段下, 在前端会提供一个
全局的 `transformResponse` 来完成对restful资源的拆包, 

**同时注意:**
- `$resource` 中定义`actions`的时候
就不需要自己写`transformResponse` 方法(会覆盖全局的方法, 并且不能保证格式), 
- 返回类型的正确, 比如对 `someObj.get({id: 1})` 时, `data` 字段的对象是个 `{}`, 而对 `someObj.query()`时, `data`字段内的对象是`Array`(`action`中 `isArray:true`)
  
 
###http status: 400 (or any other code you prefer)

```json
{
    'message': 'oops',
    'data': 'return data',
    'status': 0
}
```

`config.js`

```javascript
// global transformResponse
$httpProvider.defaults.transformResponse.push(function (data, headers, httpStatus) {
  if ( /application\/json/.test(headers()['content-type']) ) {

    // 如果 status > 300 或 status 非0, 视为有异常, 返回全部内容
    if (httpStatus > 300 || data.status !== 0) {
      // 然后请interceptor 君来处置吧
      return data;
    } else {
      return data.data || {};
    }
  } else { return data; }
  });
```

**说明:** `transformResponse` 传入的 `data` 参数实际上是 `response.data`, 这个 `response`对象之后会被传到 `interceptor`中. 此处对 `http status` 和 `status` 同时做判断, 如果不满足视为发生错误, 数据不做拆包, 直接返回原始的数据. 如果状态判断为成功的话, 取出 `data.data`, 返回给调用者的 `resolveHandler`.


`interceprot.js`

```javascript
angular.module('tutorialApp')
  .factory("myInterceptor", function ($q) {
    return {
      response: function (response) {
        /* response 进来的如果有 message, status, data 字段,
          说明 transformResponse 认为有问题, 所以没有进行修改 */
        var data = response.data;
        if (data.status && data.message && data.data) {
          if(data.status > 0) { // 做失败判断
            return $q.reject(response);
          }
        } else {  // 已经被transform成功处理的数据
          return response;
        }
        return response;
      },

      responseError: function (responseError) {
        return $q.reject(responseError);
      }
    }
  }
```

**说明:** 
- `responseError`, 错误的数据就返回`reject`
- 对`transformResponse`调戏完了的 `response`对象, 我们先判断这个数据是不是被调戏过的(被 `return data.data`), 
  如果`message, data, status`都存在的话, 视为没被调戏过, 对`status`字段值做判断 (此处只做了简单的 > 0,
  如果有复杂的情况, 可以自己写一个`service`专门对`status`的值进行判断. (此处有个潜在的问题, 如果 `data.data`中存在 `status, data, message` 的话会误判, 暂时没想到好方法, 靠后端定义数据的时候稍微注意一下吧..)
- 被调戏过的值, 就不玩了, 直接返回.


## 使用

分普通的`$http`和 `$resource` 两种情况:

### $http

`server side`

```javascript
{
data: {
    menu: [
    {
    name: "form",
    uref: "admin.form"
    },
    {
    name: "main",
    uref: "admin.main"
    },
    {
    name: "rest",
    uref: "admin.rest.list"
    },
    {
    name: "drag-and-drop",
    uref: "admin.dragAndDrop"
    },
    {
    name: "localstorage",
    uref: "admin.localstorage"
    }
    ]
},
message: "good",
status: 0
}
```

`front end:`

```javascript
angular.module('tutorialApp')
  .factory('userService', function ($http, $q) {
    return {
      getUser: function () {
        var deferred = $q.defer();
        $http.get('http://localhost:5000/todo/api/v1.0/menu')
          .success(function (data) {
            deferred.resolve(data);
          })
          .error(function (err) {
            deferred.reject(err);
          });
        return deferred.promise;
      },
    }
  });

```
`usage`

```javascript
$stateProvider
      .state('admin', {
        url: '/admin',
        controller: 'AdminCtrl',
        templateUrl: 'views/admin.html',
        abstract: true,
        resolve: {
          menu: function (userService, $q) {
            return userService.getUser().then(function (data) { // return response.data as data
              console.log('state.admin.menu.getUser.data.success');
              console.log(data.menu);
              return data;
            }, function (err) {
              console.log('state.admin.menu.getUser.data.fail');
              console.log(err);
              return $q.reject(err);
            });
          }
        }
      })
```
### $resource

`server side:`

```json
{
  "data": [
    {
      "description": "Milk, Cheese, Pizza, Fruit, Tylenol",
      "done": false,
      "id": 1,
      "title": "Buy groceries",
      "uri": "http://localhost:5000/todo/api/v1.0/tasks/1"
    },
    {
      "description": "Need to find a good Python tutorial on the web",
      "done": false,
      "id": 2,
      "title": "Learn Python",
      "uri": "http://localhost:5000/todo/api/v1.0/tasks/2"
    }
  ],
  "message": "good",
  "status": 0
}
```
注意, 此处不需要做`query`下的 `transformResponse`

`front end`

```javascript
angular.module('tutorialApp')
  .service('todolist', function ($resource, $q) {
    // AngularJS will instantiate a singleton by calling "new" on this function
    return $resource("http://localhost:5000/todo/api/v1.0/tasks/:id",
      {id: "@id"},
      {
        "query": {
          isArray: true,
        },

        "get": {
        },

        "update": {
          method: "PUT",
        }
      })
  });
```

`usage`

```javascript
angular.module('tutorialApp')
  .controller('RestEditCtrl', function ($scope, $stateParams, todolist, $state) {
    $scope.todo = todolist.get({id: $stateParams.id});

    $scope.update = function (form) {
      if (form.$invalid) {
        return;
      } else {
        $scope.todo.$update(
          function (value) {
            console.log('edit-success');
            // $state.go('admin.rest.list');
          }, function (err) {
            console.log('restEditCtrl.update.err');
            console.log(err);
          }
        );
      }
    }

  });
```
