title:  requirejs的插件介绍与制作
tags: [javascript, requirejs, regularjs]
date: 2014-10-19 15:39:17

---

{%raw%}


## 前言


我这里就不介绍[requirejs](http://requirejs.org/)了， 简而言之： requirejs是支持[AMD](https://github.com/amdjs/amdjs-api/wiki/AMD)规范的模块加载器， 事实上它也是AMD的最直接推动者。

现在可供挑选的开源模块解决方案很多，比如component、cjs+browserify、umd等等，但是无疑类似requirejs这类加载系统是现在最成熟和可靠的解决方案，所以[regularjs](https://github.com/regularjs/regular)第一步就是提供对requirejs的插件支持。



<!-- more -->


----------------


## requirejs的插件体系

requirejs的源码内部预留了hook，使得你可以创建插件来增强这个模块系统，并且这个插件可以做到影响到你的[OPTIMIZER](http://requirejs.org/docs/optimization.html)阶段，一些资源可以被处理为标准的AMD模块。

插件普遍被用来

1. 预编译   
2. 加载非js文本
3. lint 或 test 后置或前置的操作 等等

__example__
比如它本身是不支持加载文本信息的，但是你可以通过[text!](http://requirejs.org/docs/api.html#text)插件来加载。


```javascript
require(['text!foo.html', 'jquery'], function(foo, $){
	$('#anchor').html(foo);
})
```

需要注意的是由于文本无法用script标签进行加载，所以text内部是通过XHR来载入的，即它会受到同源策略的影响。


__优化OPTIMIZER__

由于requirejs同时提供工具(npm:requirejs)可以静态打包优化AMD，刚才的那个`text!foo.html`会同时被__text插件__转换为类似下面的AMD模块结构

```javascript
define('text!foo.html',[],function () { 
	return '<h2>早上好\n</h2>';
});

```

requirejs的插件其实是一个[实现的特定接口](http://requirejs.org/docs/plugins.html#apiload)的标准AMD模块，它在定义时与其它业务模块并无区别。

例如官方text插件的源文件

```javascript

define(['module'], function (module) {
    'use strict';
    var text = {
	    load: function(){}
	    ....
    }
    return text
})
```

其中load等接口是插件必须实现的，


对于各个接口描述我就不细究了，大家可以参考官网


__顺便列举一些有用的requirejs插件__

1. [text插件](http://requirejs.org/docs/api.html#text)(最常用插件)
	如果你的文本内容无需在打包优化阶段做处理，几乎都可以使用这个插件来完成加载
2. [json插件]()
	比楼上多做了一步JSON.parse.
3. [amd-loader](http://requirejs.org/docs/api.html#pageload)(好东西): 
    注意不要requirejs本身弄混了，因为requirejs本身不是基于xhr的，这个插件主要是提供完善的xhr支持来加载文本内容。一句化即它是__[插件的loader插件]__，作者事后才发现有这么一个插件...绕了不少弯路。具体例子可以查看[es6](https://github.com/guybedford/es6)
4. [handlebars](https://github.com/SlexAxton/require-handlebars-plugin)
     用来加载handlebar的插件



其实由于amd等模块系统占据了开发中的模块入口这一环，其实在开发中可以有无限的可能性，这也是常规大公司都会自造一个轮子来最优配置的缘由之一，事实上requirejs目前的插件系统已经有足够的灵活性来定制自己的策略。


###实现requirejs-regular的过程


__背景__

首先我们先理清我们的需求, 与常规的的模板预编译类似，我们的插件主要为了实现两个功能。

1. 在开发阶段，我们希望能加载js文件一样，加载我们的模板文件，这带来的几个好处
   - 这使得我们不必将模板零散的填充到页面的script 或 textarea标签中
   - 依赖系统唯一化, 模板依赖集成进了模块依赖中
2. 在优化阶段(即requirejs提供的[OPTIMIZER](http://requirejs.org/docs/optimization.html)的上线打包功能)，我们的模板字符串可以被预处理为序列化的AST对象，这样就不会发生浏览器端的解析，效率更高。

__实现__

一个插件模块会同时跑在浏览器端(开发环境)和node端(为线上或测试环境的打包优化工具)，所以你的插件模块必须可以同时跑在浏览器端和node端，这个几乎是整个开发环境最麻烦的一部分

1. regular.js的单文件虽然是[umd](https://github.com/umdjs/umd)模块可以支持amd环境，但是由于依赖的dom。所以首先要将parser部分(不依赖dom)打成一个AMD模块，由于regularjs本身就是基于commonjs的模块构建，将其中一部分打成AMD模块是分分钟的事情，这里我们使用[webpack](https://github.com/webpack/webpack)来打包成[regular-parser.js](https://github.com/regularjs/regular/blob/master/dist/regular-parser.js)，简单起见我们随regularjs模块一同发布到bower上
2. 我们还要解决模板的加载问题，插件内部的加载问题也要手动解决，即你至少要实现[loader](http://requirejs.org/docs/plugins.html#apiload)接口和get接口。这里我们完全可以偷个懒，直接使用__!text插件__。

即插件会依赖这两个模块

```javascript
define(['text', 'regular-parser'], function(text, parser){
	//blalalalala...
	return{
		load: load,
		write: write
	}
})
```


然后我们只需要实现两个接口:

- __load__ 

```javascript
var buildMap = {};

function load(name, req, onLoad, config){
    text.load(name, req, function(data,r){
        onLoad(
          (buildMap[name] = parser.parse(data, false))
        );
    }, config);
}

```

这里直接使用了text插件的纯文本加载，需要注意的是这个onLoad接口，传入参数相当于模块的内容，我们这里预parse了这段文本内容。即你通过`rgl!template.html`最终会获得解析后的AST数据。

其实对于regularjs来讲在浏览器端有无进行模块系统层面的预解析并无意义，关键是在打包优化阶段。这里的__buildMap__主要是为了保存这段内容用于打包使用。

__write__
实现write接口主要是为了在打包优化阶段改写相关模块

```javascript
var tpl = function(str, data){
    return str.replace(/\{\{(\w+)\}\}/g, function(all, name){
        return data[name] || ""
    })
}
var template ='define("{{pn}}!{{mn}}",function(){ return {{ast}} });\n';
function write(pn, mn, writeModule){
   if(buildMap[mn]){
       writeModule(
           tpl(template,{
               pn: pn,
               mn: mn,
               ast: parser.parse(buildMap[mn])
           })
       )
   }
}
```



此时这个插件必须依赖于两个模块，即必须同时保证`text`和`regular-parser`模块同时存在，类似的方案可以查看[hogan](https://github.com/millermedeiros/requirejs-hogan-plugin)，它必须保证环境中有`hogan`和`text`才可以运行. 熟悉requirejs打包过程的同学也知道，除了loader端的配置，我们在build的打包文件也需要一并将这些依赖模块剔除，因为上线时是不需要这些插件的。__所以这将大大增加配置成本__，其实解决方案也很简单，就是使用[webpack]再将其打包成一个standlone的AMD模块即可，具体可以参考[这里的gulpfile](https://github.com/regularjs/requirejs-regular)。


__大功告成__

使用就非常简单了，和你使用[requirejs-text]()差不多, 


1.首先下载[rgl.js](https://github.com/regularjs/requirejs-regular#download)，最简单的就是bower安装

```
bower install regularjs-regular --save
```
_save参数是安装后并写入到bower.json中，这个和npm一致_

2.配置
```
require.config({
   paths : {
       "rgl": '../../bower_components/regularjs-regular/rgl'，
       // 同时载入我们的regularjs来使用这些模板
       "regularjs": '../../bower_components/regularjs/dist/regular'
   }
});
```

3.使用

```javascript
require(['rgl!./foo.html', 'regularjs'], function( tpl, Regular){

    var Foo = Regular.extend({
      template: tpl
    })

    new Foo({}).$inject("#app")

});
```


4.打包


模板文件`<h2>{{message}}</h2>`经过插件处理后会打包成

```javascript
define("rgl!foo.html",function(){return [{"type":"element","tag":"h2","attrs":[],"children":[{"type":"expression","body":"_c_._sg_('message', _d_['message'])","constant":false,"setbody":"_c_._ss_('message',_p_,_d_, '=')"}]}] });

```

即上线后就不会有parse了，比如PO主目前正在开发的项目在初期就有几十个模板文件，build成单文件后的运行时开销还是应该尽量避免.

__tip：build.js记得通过__stubModules__配置项目删除掉这个插件模块，具体看demo的[build.js](https://github.com/regularjs/requirejs-regular/blob/master/demo/build.js)。__



###对于NEJ的使用者

__NEJ的新模块系统支持上述类似的regular模板加载了__

网易杭州的同事，事实上你已经可以在NEJ的__新模块系统中__（完全兼容老版本）通过`regular!path/to/template.html`的方式来加载你的regular模板了，打包之后模板将会被预解析，同时新版NEJ也支持`text!`加载纯文本内容, 详询__@飞锅__。新版本的加载系统，支持类似AMD的注入写法，并且兼容老版本的模块写法，亲测好用哈。


{%endraw%}
