
title:  超简单工具puer——“低碳”的前后端分离开发
date: 2014-10-24 16:39:51
tags: ['tool', 'nodejs', 'javascript']


---

前几天，跟一同事([MIHTool](http://www.mihtool.com/)作者)讨教了一下开发调试工具。其实个人觉得相较于定制一个类似MIHTool的Hybrid App容器，基于长连的B/S架构的工具其实会是一个更轻量的解决方案。此文会分享一下超简单工具[puer](http://github.com/leeluolee/puer)，以及如何利用它在产品开发的各阶段实现高效的开发。

本文title有点大哈，相较于目前国内正流行起来的前端后移的前后端分离方案，本文介绍是一种“低碳”的版本，所以不要纠结于这个Title的正确性哈。

<!-- more -->

-------------



##简介

简而言之，__Puer是一个可以实时编辑刷新的前端服务器__。特性一览:

- 提供一个当前或指定路径的静态服务器
- 所有浏览器的实时刷新：编辑css实时更新(update)页面样式，其它文件则重载(reload)页面
- 提供简单熟悉的mock请求的配置功能，并且配置也是自动更新。
- 可用作代理服务器，调试开发既有服务器的页面，可与mock功能配合使用
- 集成了[__weinre__](http://people.apache.org/~pmuellr/weinre-docs/latest/)，并提供二维码地址，方便移动端的调试
- 可以作为connect中间件使用(前提是后端为nodejs，否则请使用代理模式)

可以发现这里功能并不多，但罗列的基本都是实际前端开发中会涉及到的。可能敏锐的朋友会点出，同类的[browser-sync](https://github.com/shakyShane/browser-sync)提供了更强的操作同步的功能。这点其实主要不想去盲目攀比功能(实现其实并不难，因为基础的注入脚本已经做了，剩下的都只是功能堆砌)，目前的出发点都是作者实际的需求(好吧，其实是懒)。还有就是，其实__puer诞生比它要早一年多__。


## 让我们开始使用吧

###安装

确保你安装了[nodejs](http://nodejs.org/download/)（现在还有没nodejs环境的前端？ 拖出去喂狗吧）

使用npm全局安装`puer`命令

```shell
npm install puer -g
```

输入`puer -h`可以查看Help

```shell
Usage:	puer [options...]

Options:
  -p,--port 	server's listen port, 8000 default
  -f,--filetype 	fileType to watch(split with '|'), defualt 'js|css|html|xhtml'
  -d,--dir 	your customer working dir. default current dir
  -i,--inspect   	start weinre server and debug all puer page
  -x,--exclude   	exclude file under watching(must be a regexp), default: ''
  -a,--addon 	your addon's path
  -t,--target 	proxy url
     --no-reload   	close  auto-reload feature,(not recommended)
     --no-launch   	close the auto launch feature
  -h,--help   	help list
```

强烈中文即时感的英语相信和大家交流起来不会很困难。

__使用__

90%的使用场景下
```shell
cd /path/to/workspace ↵
puer ↵
```

puer会默认为你打开http://localhost:8000页面(端口可以 `-p 8001`参数进行控制)，并贴心的帮你列出了所有你本地可用的ip以及当前的地址二维码，方便手机访问。

编辑当前路径下的文件，会实时更新页面(无论你在多少台电脑打开多少个页面)。


![puer使用1](http://leeluolee.github.io/attach/2014-10/puer-step-1.gif)

关于如何命令行下快速到达工作目录，作者推荐两个工具

1. [z](https://github.com/rupa/z): 必装shell工具，快速进入最常用目录
2. [sublime-terminal](http://wbond.net/sublime_packages/terminal): 直接进入当前文件或工程的所在路径

在开发静态页面时，这个简单的功能意义非凡，特别是双屏的时候，可以完全解放你的F5键。关键是：

> 保存后看到浏览器同步刷新，有种莫名的愉悦感涌上心头有木有？

作者简单将这种现象归结于之前看到的一篇文章：【人无法在两种思维模式下进行高效切换】，猜想这可能这和CPU的进程切换是一样一样的。


### 进阶1 mock请求

假设你的静态页面开发到一定程度，需要与服务器端交互了，而后台服务还完全无法联调，这其实是属于最简单的__前后端分离开发__的场景。一般而言， 作者会通过以下几种方案。

__层级1: 通过代码解耦，直接在前端mock数据__

这种方式影响较大，而且无论你解耦的如何，都会增加最终上真实环境的切换成本。

__层级2: 通过fiddler等调试代理工具mock数据__

优点是到正式环境的切换成本小，但__配置成本较大__，也接口mock也有局限性并基本上只能是静态数据模拟。

__层级3：利用puer无痛的解决这个问题__

puer提供了叫插件(addon)的功能，集成了express的route.js来达到最简的路由配置，可以提供基于真实http请求与相应的动态的mock数据。

```shell
puer -a route.js
```

route.js 这么写

```javascript
// use addon to mock http request
module.exports = {
  // GET
  "GET /v1/posts/:id": function(req, res, next){
	// response json format
    res.send({
      title: "title changed",
      content: "tow post hahahah"
    })

  },
  // PUT POST DELETE is the same
  "PUT /v1/posts/:id": function(){

  },
  "POST /v1/posts": function(){

  },
  "DELETE /v1/posts/:id": function(){

  }
}%
```

它其实就是一段nodejs程序，输出是一配置对象，key的空格前代表的是请求Method，后半部分是请求路径，而value代表回调函数和express的路由中间件是一致的，传入的request和response对象，相较于原生的提供了一些额外方法，不了解的也可以[戳这里](http://expressjs.com/3x/api.html#req.params)


__【<a href="http://leeluolee.github.io/attach/2014-10/puer-step-2.gif" target="blank">戳这里看演示GIF</a>】__

通过演示其实你可以发现，编辑route.js后，这份配置脚本是动态更新的，免去了你一次次重启服务器的工作。

###进阶2：使用代理模式，应用puer到以有服务器中

开发进行到一定阶段，一般后端服务就逐步进入了，静态页面的开发不再适用。到这个时候，我们可以使用`--target path`切换到代理模式将puer作为一个代理服务器使用。

比如本地已经存在一个localhost:8020的服务，你要在其上实现自动刷新的功能，请使用`-t 或 --target`。


```shell
puer -t http://localhost:8020
```

__[【请戳演示GIF】](http://leeluolee.github.io/attach/2014-10/puer-step-3.gif)__

需要注意的是，进阶2提到的addon是可以和这个模式配合使用，这使得我们在后台服务青黄不接的时候率先开发完前端功能，深藏功与名。


```shell
puer -t http://localhost:8020 -a route.js
```

__[【请戳target配合addon的演示】](http://leeluolee.github.io/attach/2014-10/puer-step-4.gif)__

###进阶3： 使用weinre调试所有经过puer的页面

weinre是目前最常用的远程调试工具，它虽然几乎无法调试JS，但是由于和平台、浏览器无关的特性，使得这个低效的工具一直流行至今，也有很多工具集成了它，比如我之前提到的MIHTool。

不过要使用weinre，你需要在每个调试的页面插入一个记不住名字的脚本，并开启weinre服务，这都属于重复性劳动。puer通过 `-i` flag来开启 weinre的内置集成，每一个经过puer的页面都会被自动注入脚本。你可以在9001端口找到你的weinre服务，也可以通过puer的初始页面进入

```bash
puer -i
```

__[【请戳inspect的演示】](http://leeluolee.github.io/attach/2014-10/puer-step-5.gif)__

###进 阶4： 作为中间件来应用与nodejs作为后台的产品
这个功能其实并不常用了，它将puer作为express/connect服务器的中间件来使用，先贴个代码范例。

```javascript
var connect = require("connect")
var path = require("path")
var http = require("http")
var puer = require("puer")
var app = connect()
var server = http.createServer(app)

var options = {
    dir: "path/to/watch/folder",
    ignored: /(\/|^)\..*|node_modules/  //ignored file
}

app.use(puer.connect(app, server , options))   //use as puer connect middleware
// you must use puer middleware before route and static midleware(before any middle may return 'text/html')
app.use("/", connect.static(__dirname))


server.listen(8001, function(){
    console.log("listen on 8001 port")
})

```
一般你在`development`环境开启它，而在`product`环境关闭即可，__需要注意的是它必须放在可能输出html的中间件之前。__

------------


一个简单的`puer`命令可以带你在各个开发阶段自由飞翔，还不赶紧试一下？

##写在最后

__名字含义__ ： puer意为普洱，除了爱喝和够短没有任何含义，懒得取名罢了。

写puer的原动力是作者对[f5网页免刷新工具](http://www.getf5.com/)欲求不满（况且它如此简单的功能还需要开启一个桌面gui完全不能忍是么），所以功能抉择上带有一些主观性。如果你试用后，觉得它有价值并有改进余地，可以在[issues](https://github.com/leeluolee/puer/issues)下留下你的灼见(__必须先来一发star__)。

_碰到问题先`puer -h`看看有没有你的答案_


###资源汇总


1. __[puer（推荐）](https://github.com/leeluolee/puer)__: “低碳”的前端服务器工具
2. [weinre(推荐)](http://people.apache.org/~pmuellr/weinre-docs/latest/):  常用远程调试工具
3. [z(推荐)](https://github.com/rupa/z):  Shell工具，快速跳转到最常用的目录。
4. [browser-sync](https://github.com/shakyShane/browser-sync): 包含puer的部分功能，同时提供多页面的操作同步(跳转、表单)
5. [MIHTool](http://www.mihtool.com/):MIHTool是前端工程师在iOS设备上调试和优化页面的得力助手（原slogan）
6. [sublime-terminal](): 快速打开sublime工程或当前文件目录
7. [f5网页免刷新工具](http://www.getf5.com/)：基于air的免刷新gui工具，习惯windows的gui工具的童鞋可以试用下。
