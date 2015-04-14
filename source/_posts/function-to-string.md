title: 关于Function.prototype.toString的野生小技巧
date: 2015-04-13 11:46:36
tags: [ "javascript" ]
categories:
---



__⚠约定__: 以下将`Function.prototype.toString`简称为`fn.toString`

_我发现自己快没救了，又把一个短文硬生生写成了长文，客官慢用..._

--------------

__先介绍下fn.toString呗__

> The toString() method returns a string representing the source code of the function.
> --- from [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/toString)

即这个原型方法可以帮助你获得 __函数的源代码！！__, 比如

```js

function hello ( msg ){
  console.log("hello")
}

console.log( hello.toString() );

```

输出:

```js
'function hello(){ \
  console.log("hello") \
}'
```

这个方法真是碉堡了..., 通过合适的正则, 我们可以从中提取出丰富的信息.

- 函数名
- 函数形参列表
- 函数源代码

这些信息提供了javascript意想不到的灵活性, 我们来看看野生的例子吧.

## 提取AMD模块定义里的依赖列表.


熟悉AMD或者被CMD科普过的同学应该知道，AMD中是这样定义模块的.

```js
// 模块c的定义
define( ['a', 'b'] ,function ( a, b ) {

  return {
    action: function(){
        return a.key + b.key;
    }
  }
});

```

当此模块加载完成的同时define函数将被运行，传入依赖列表的`'b'`和`'a'`指导模块加载器需要先获得他们的模块定义, 并以参数形式注入到`c`模块的factory函数. 所以明确声明的`['a', 'b']`依赖列表至关重要，它指导模块下一步的策略.


事实上，AMD规范中也定义了一种叫[simplified commonjs wrapping](https://github.com/amdjs/amdjs-api/wiki/AMD#simplified-commonjs-wrapping-)的写法, 可以以类commonjs的写法来定义一个模块.

```js
define(function (require, exports, module) {
  var a = require('a'),
      b = require('b');

  exports.action = function () {
    return a.key + b.key;
  };
});

```

依赖变成了【使用注入到模块的`require`函数引入】(如`require('a')`), 但是这就带来了一个问题, __如何获得此模块的依赖列表?__

答案当然是使用`function.toString`.

```js

var rRequire = /\brequire\(["'](\w+)["']\)/g;

function getDependencies( fn ){
  var map = {};
  fn.toString().replace(rRequire, function(all, dep){
    map[dep] = 1;
  })
  return Object.keys(map);
}


getDependencies(function(require, exports){

    var a = require("a");
    var b = require("b");

    exports.c = require("a").key + b.key;
})

// => ["a", "b"]


```

输出`["a", "b"]`, 我们成功获得依赖列表.

当然，这里的正则是简化版的，实际要处理的情况要复杂的多，比如你至少要过滤掉注释里的信息.


## 多行字符串

关注ES6的同学应该知道， 在ES6中新增一个特性叫[Template String](https://babeljs.io/docs/learn-es6/#template-strings), 除了支持插值可以获得微弱的模板能力之外，它还有一个能力就是

> __支持多行字符串的定义__

这个在你定义多行模板字符串的时候非常有用, 可以避免不直观的字符串拼接操作.

```

var template = `
<div>
   <h2>{blog.title}</h2>
   <div class='content'>{blog.content}</div>
</div>
`

```
这个等同于

```
var template = "<div>" +
   "<h2>{blog.title}</h2>" +
   "<div class='content'>{blog.content}</div>"+
"</div>"
```

是不是清晰方便了很多. __可问题是ES6现在正式来了吗？__ 没有...

Duang~ `function.toString`又闪亮登场, 一解我们青黄不接时的尴尬.

```js
var rComment = /\/\*([\s\S]*?)\*\//;
// multiply string
function ms(fn){
  return fn.toString().match(rComment)[1]
};

ms(function(){/*
  <div>
    <h2>{blog.title}</h2>
    <div class='content'>{blog.content}</div>
  </div>
*/})
```

将会输出下面这段字符串

```html
<div>
   <h2>{blog.title}</h2>
   <div class='content'>{blog.content}</div>
</div>
```

事实上github上已经有一个[无聊的库](https://github.com/sindresorhus/multiline)做了这件事，并获得了1200个star...,
我记得13年做内部系统时就已经用上了这个trick, 所以现在做什么都得快, 勿以点子小而不为.

_题外话: 管理模板当然有更好的解决方案，可以参考 [Regularjs指南:如何优雅的管理你的模板](http://regularjs.github.io/guide/zh/template/README.html)_



## 基于形参约定的依赖注入

Angular里有个很大的噱头就是它的依赖注入。

假设现在有如下一段Angularjs的代码，它定义了2个factory:`greeter`和`runner`, 以及controller`MyController`.

```js
angular.module('myApp', [])
.factory('greeter', function() {
  return {
    greet: function(msg) { alert(msg); }
  }
})
.factory('runner', function() {
  return {
    run: function() {  }
  }
})
.controller('MyController', function($scope, greeter) {
  $scope.sayHello = function() {
    greeter.greet("Hello!");
  };
});

```

注意这个controller会在angular内部compile遇到节点上的某个指令比如`<div ng-controller="MyController">`时被调用.

现在问题来了, __angular如何知道要传入什么参数呢__？ 比如上例中的controller其实是需要两个参数的.

__答案是基于形参名的推测__


你可以先简单理解为在每次调用factory等函数时, 对应的定义会缓存起来，例如

```
var cache = {
  greeter: function(){

  },
  runner: function(){

  }
}

```

既然如此，现在要做的就是获得依赖, `function.toString`可以帮助我们从形参中获得这些信息


```

var rArgs = /^function\s*[^\(]*\(\s*([^\)]*)\)/m;
function getParamNames( fn ){
  var argStr = fn.toString().match(rArgs)[1].trim();
  return argStr? argStr.split(/\s*,\s*/): [];
}

getParamNames(function( $scope, greeter ){})

// ["$scope", "greeter"]

```

输出`["$scope", "greeter"]`, 也就意味着我们获得了依赖列表， 这样我们就可以从cache中获得对应的定义了.


__即便如此，作者仍然觉得angular式的依赖注入其实完全是一种设计过度, 无论是基于推断还是显示声明__


## 继承中的`super()`实现.

在ES6规范中，终于引入了语言级别的`class`支持
我们先来看下教科书版本的javascript继承的实现





## 序列化函数

注意的由于


new Function()， 可以帮助我们将这段代码， 当然

> 自己做好安全措施哦


看似我们这么一来一回，什么都没有干， 事实上


### 应用1: webworker



### 应用2: 服务器和客户端的逻辑共享


- webworker: http://stackoverflow.com/a/19655815
- 前后台函数服用



## 运行时的文档注释

需要注意的时， 在js解释时， 开发者对于注释信息是未知的， 但是如果注释在函数里时， 就不一样了
这个可以帮助我们不通过词法解析就可以获得文档, 并且是runtime的
https://msdn.microsoft.com/en-us/library/hh874692.aspx

```
```

## 函数级别程序自修改


>
