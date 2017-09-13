title: 关于Function.prototype.toString的野生小技巧
date: 2015-04-13 11:46:36
tags: [ "javascript" ]
categories:
---



__⚠约定__: 以下将`Function.prototype.toString`简称为`fn.toString`

_我发现自己快没救了，又把一个短文硬生生写成了长文，客官慢用..._


## __先介绍下Function.protoype.toString__

> The toString() method returns a string representing the source code of the function.
> --- from [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/toString)

即这个原型方法可以帮助你获得 __函数的源代码！！__, 比如

<!-- more -->

```js

function hello ( msg ){
  console.log("hello")
}

console.log( hello.toString() );

```

输出:

```js
'function hello( msg ){ \
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

```js

var template = `
<div>
   <h2>{blog.title}</h2>
   <div class='content'>{blog.content}</div>
</div>
`

```
这个等同于

```js
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

因为在通过`fn.toString()`的时候， 同时会保留函数中的注释，但是注释是不会被执行的，所以我们可以安全的在注释中写一些非js语句，就比如html.

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


```js

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


10个前端9个看了js高程那本红书吧，我们先来看下教科书版本的js继承的实现

```js
// 基类
function Mesh(){}

function SkinnedMesh( geometry, materials ){
  Mesh.call( this, geometry, materials )
  // blablabla...
}
// 避免new Mesh，带来的两次构造函数调用
SkinnedMesh.prototye = Object.create(Mesh.prototype)
SkinnedMesh.prototye.constructor = Mesh;


// other

SkinnedMesh.prototype.update = function(camera){
  Mesh.prototype.update.call(this, camera);
}

```

这种继承方式足够用，但是有几个问题.

- 调用父类函数真的足够繁琐
- 一旦父类发生改变，所有对父类的调用都要改写
- 从编程逻辑上看， 这种类式继承不够直观


如果是下面这种方式呢？


```js
var SkinnedMesh = Mesh.extend({
  // 履行构造函数职责
  init: function( geometry, materials ){
    // 由于super是关键字，修改为supr
    this.supr( geometry, materials ); // 调用父类同名方法
  },
  update: function( camera ){
    this.supr() // 调用Mesh.prototype.update
  }
})
```

_上面这段写法其实就是来自于著名的[ded/klass](https://github.com/ded/klass)_

是不是直观了很多， 事实上上面这段代码修改为使用coffeescript等语法噪音弱的语言来书写，已经非常接近与有关键字支持的语言了.
但相信不少人还是会疑惑, 为什么在`init`和`update`中调用`this.supr()`为什么可以准确定位到父类不同的方法？

其实，在extend的同时就已经在查找规则封装好了, 让我们将这个问题简化为两个对象间的继承。

```js
function extend(child, parent){
    for (var i in child ) if (child.hasOwnProperty(i) ){
      wrap(i, child, parent)
    }
    return child;
}

var rSupr = /\bsupr\b/
function wrap(name, child, parent){
  var method = child[name],
    superMethod = parent[name];

  // 我们通过fn.toString() 打印出方法体，并确保它使用的this.supr()
  if( rSupr.test( method.toString() ) && superMethod) {
    superMethod = superMethod.bind(child);
    child[name] = function(arguments){
        // 保证嵌套函数调用时候正确
        var preSuper = child.supr;
        child.supr = superMethod;
        method.apply(this, arguments);
        child.supr = preSuper
    }
  }
}

var mesh = {

  init: function(){
    console.log( "mesh init ");
  },
  update: function(){
    console.log(" mesh update");
  }
}

var skinnedmesh = extend({

  init: function(){
    this.supr()
    console.log( "skinnedmesh init ");
  },
  update: function(){
    this.supr()
    console.log(" skinnedmesh update");
  }
}, mesh)

skinnedmesh.init();
skinnedmesh.update();
```

输出

```sh

mesh init
skinnedmesh init
mesh update
skinnedmesh update
```

其中, fn.toString()输出方法源码， 并通过正则判断是否源码中调用了supr(). 如果是就包一层函数用来动态的制定this.supr对应的方法。

是不是挺奇妙的构想？事实上由于方法的包裹是发生在extend时，在方法运行时，是没有查找开销的，所以很多框架都使用这个技巧来实现一个简化的继承模型. 包括[Regularjs](https://github.com/regularjs/regular)

__题外话:__ 再往前看一点，在ES6规范中，已经引入了语言级别的`class`支持

```js
class SkinnedMesh extends Mesh {
  constructor(geometry, materials) {
    super(geometry, materials);

    //...
  }
  update( camera ) {
    //...
    super.update( camera );
  }
}
```


注意构造函数里的super和update里的`super()`以及`super.update()`分别用来调用父类的构造函数和实例方法, 相当于


```js
Mesh.call(this, geometry, materials)

Mesh.prototype.update.call(this)
```

但还是那个问题: __ES6目前可用了吗?__



## 序列化函数

什么是函数序列化，即将函数序列话成字符串这种通用数据格式 这样可以实现__程序逻辑在不同的runtime之间传递__

我们这里点一个应用场景: 不依赖外部js文件时仍能使用webworker帮助我们进行并行计算

### 什么是webworker

在浏览器中， js的执行与UI更新是公用一个进程, 这导致它们会互相阻塞, 用户直接的感受就是, 在长时间的脚本执行中，界面会“卡住”.

特别在很多处理大列表的场景中，熟练的程序员会通过(setTimeout/setInterval/requestAnimationFrame)等方法来模拟任务分片，__好为UI线程腾出时间__, 这样用户的体验就是按钮可以点了，但总的完成时间其实是增加了

有没有一种一劳永逸的方法呢？ __webworker__

> Web Workers provide a simple means for web content to run scripts in background threads.
> --- [MDN: webworker](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Using_web_workers)

即我们可以将耗时的计算任务放置在后台运行， 完成之后通过事件来通知主线程, __注意它会真正生成系统级别的线程，而不是模拟出来的__。


_事实上，worker分为专用worker和共享worker，我们只会涉及到前者_


我们来个耗时的例子，第一个映入我脑帘的就是计算斐波那契数列, 足够简单但是足够耗时, 就它了。

```html
<div>
  <input type="text" id="num" placeholder="请输入你要计算的数值" value=40>
  <button onclick="compute()">使用worker计算</button>
  <button onclick="compute(1)">不使用worker</button>
</div>
<hr/>
结果: <output id="result"></output>

<button>点我看看UI线程阻塞了吗</button>

<script>
  var worker = new Worker('mytask.js');
  var vnode = document.getElementById("num");
  var rnode = document.getElementById('result');

  function compute(noWorker) {
    var value = parseInt(vnode.value || 0, 10) ;

    if(noWorker){
      console.time("fibonacci-noworker")
      rnode.textContent = fibonacci( value );
      return console.timeEnd("fibonacci-noworker")
    }

    console.time("fibonacci-worker")
    worker.postMessage( value );
  }


  worker.onmessage= function(e) {
    rnode.textContent = e.data;
    console.timeEnd("fibonacci-worker");
  }

  function fibonacci(n) {
    if(n < 2) return n;
    return fibonacci( n - 1 ) + fibonacci(n - 2);
  }

</script>

```

对应的mytask.js,如下

```js

onmessage = function( ev ){
  self.postMessage( fibonacci( ev.data ) );
}

function fibonacci(n) {
  if(n < 2) return n;
  return fibonacci( n - 1 ) + fibonacci(n - 2);
}

```

mytask.js与worker.html的文件结构如下.

```shell

└── folder
  ├── mytask.js
  └── worker.html

```



打开worker.html, 分别点击两个按钮, 你会发现控制台输出结果是这样的.

```js
fibonacci-worker: 1299.735ms
fibonacci-noworker: 5198.129ms
```

使用worker的版本速度会更高一些， 当然更关键的问题是 __noworker版本阻塞的UI线程，使得button等控件都没有反应了__.


### 使用function.toString实现单文件的Webworker运算

但是, __非worker版本有个好处就是逻辑定义都在一个文件里__， 而不用分散计算逻辑到子文件， 有没有两全的方案呢？

答案是 __使用`function.toString()` 和 `URL.createObjectURL` 方法来动态创建脚本文件内容__.

我们对worker.html做以下调整

```html
<div>
  <input type="text" id="num" placeholder="请输入你要计算的数值" value=40>
  <button onclick="compute()">使用内联式的worker计算</button>
</div>
<hr/>
结果: <output id="result"></output>

<button>点我看看UI线程阻塞了吗</button>

<script>
  function workerify(fn) {
    var worker = new Worker(
        URL.createObjectURL(new Blob([
           'self.onmessage = ' + fn.toString()], {
           type: 'application/javascript'
        })
    ));
    return worker
  }

  function compute(noWorker) {
    var value = parseInt(vnode.value || 0, 10) ;
    worker.postMessage( value );
  }

  var worker = workerify(function(e){
    function fibonacci(n) {
      if(n < 2) return n;
      return fibonacci( n - 1 ) + fibonacci(n - 2);
    }
    return self.postMessage( fibonacci(e.data) )
  })


  var vnode = document.getElementById("num");
  var rnode = document.getElementById('result');

  worker.onmessage = function(e){
    rnode.textContent = e.data;
  }

</script>

```

这一次，我们不再需要mytask.js了，因为这个文件内容其实已经通过 `URL.createObjectURL` 和 `Blob`创建出来了. 关于这两个API的详细描述，大家请自行MDN，再延伸下去，此文就没法控制了。



## 总结

其实`fn.toString()`所有的能力都归结为它可以得到函数源码，配合new Function(), 事实上还可以产生更大的可能性. 比如我们可以将服务器端的逻辑传递到客户端，
而不仅仅只是传递数据.
