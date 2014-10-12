title: 前端模板技术面面观
date: 2014-10-10 18:59:49
tags: [regularjs, javascript]
categories:
---
{%raw%}

## 此文缘由

其实从发布[regularjs](http://github.com/regularjs/regular)之后，我发现在google搜索regularjs 不是给我这个画面

就是给我这个画面

突然发现取名字真是个大学问，当时就基本预计到了会有不明真相的朋友认为它只是一个照搬[angularjs](http://angularjs.org)的轮子。

在这个文章，我**不会直截了当去与angular做直接的对比**，而是从最基本原理开始对现有的**模板解决方案**进行一个全面的分类，同时会给出它们的一些或优或劣的特性，这些特性基本都是本质性的，即它不为**维护者的水平高低和勤勉与否**所限制，所以是具有客观性的。

> 此文的写作耗时很长，称之为雄文不为过，小心慢用


## 什么是模板解决方案？

你可以先简单的理解为模板引擎。

事实上前端的模板解决方案已经从 **“选出一个好用的模板好难”** 发展到了 **“从这么多模板中选一个好难的”**的阶段，[Template-Engine-Chooser!](http://garann.github.io/template-chooser/)似乎也开始无法跟上节奏了。再加上目前Dom-based的模板技术的崛起(angularjs, knockout等)，渐渐让这个领域有乱花渐欲迷人眼的感觉。

这篇文章会对当今前端界的三种截然不同的模板方案做一个全面的对比，它们分别是

1. **String-based 模板技术** (基于字符串的parse和compile过程)
2. **Dom-based 模板技术** (基于Dom的link或compiler过程)
3. 杂交的**Living templating 技术** (基于字符串的parse 和 基于dom的compile过程)

同种类型的模板技术的**可能性**都是相同的，即vuejs如果愿意可以发展为angularjs的相同功能层级。

(*注: 其实这么说作者后续思考后觉得并不是很妥当，因为决定这里框架的还有重要一环就是它们的数据管理层:，比如是基于脏检查还是基于setter和getter，就会有截然不同的定位*)

另外需要注意的是任何一种类型的模板技术都是不可被替代的，它们甚至可以结合使用，并且很长一段时间内还会继续共存下去。



除此之外另外一种奇葩模板技术本文也会提到即**react**，了解后你会发现它的特性更接近于Living templating。


在进入介绍之前，我们需要先过一下 **InnerHTML**，它是本文的关键因素，所以不得不说。


## innerHTML

我不认为我还需要从`innerHTML`的细节讲起，我们对它太熟悉了，那就直接从优劣开始讲吧！


### innerHTML 毫无疑问是好的

在`innerHTML`正是成为 [web 标准](https://domparsing.spec.whatwg.org/#innerhtml) 前，它当之无愧的已经是大家公认的事实标准。 这是因为：


__1 . 它便于书写并且直观__

想象下你必须添加如下的html到你的文档里

```html
<h2 title="header">title</h2>
<p>content</p>
```
直接使用 `innerHTML`
```
node.innerHTML = "<h2 title="header">title</h2><p>content</p>"
```
在对比使用`Dom API`

```javascript
var header = document.createElement('h2');
var content = document.createElement('p');
h2.setAttribute('title', 'header');
h2.textContent = 'title';
p.textContent = 'content';
node.appendChild(header);
node.appendChild(content);
```

`innerHTML` 毫无疑问赢得了这张比赛.

尽管部分框架例如[mootools:Element](http://mootools.net/docs/core/Element/Element#Element:constructor) 提供了高效的API来帮助你创建dom结构，`innerHTML`仍然会是大多数人的最佳选择

__2 . 它很快，特别在[old IE](http://www.quirksmode.org/dom/innerhtml.html)__

> 随着浏览器的发展，这个测试可能越来越不符合实际，`innerHTML`和 `Dom Level 1`创建dom结构的差距正变得原来越小  

__3. 它完成进行了String -> Dom的转换__

这个论点有点拗口，事实上后续要提到的两类模板技术都是因为这个特点而与其有了强依赖

-----
然而我们又清楚的被告知: 
> _The recommended way to modify the DOM is to use the DOM Level 1 API._
 ——Chapter 15 of "Javascript: The Definitive Guide_"

为什么？

### innerHTML 有时候又是不听话的

__1. 安全问题__

`innerHTML` 具有安全隐患.,例如:
```
document.body.innerHTML = "<img src=x   onerror='alert(xss)'/>"
```

我知道像你这样优秀的程序员不会写出这样的代码，但当html片段不完全由你来控制时(比如从远程服务器中)，这会成为一个可能引爆的炸弹。

__2. 它很慢__

等等，你刚才说了它很快！
是的，但是如果你仅仅为了替换一个属性而用`innerHTML`替换了所有的Dom节点，这显然不是一个明智的决定，因为你深知这是低效的。所以说:

> Context is everything

所有离开背景谈的性能、功能、性功能都是伪科学

__3. 它很笨__

它会完全移除所有现有的Dom，并重新渲染一遍，包括事件和状态都以不复存在，这点利用innerHTML来进行render的框架(例如Backbone)的开发者应该深有体会，为了减少损失，不能不把View拆的越来越细，从而抱着看似“解耦完美”的架构体系进入了维护的深渊。

_注: 其实react的最大贡献就是它差不多是提供了一个更smart的innerHTML解决方案。_
    
__4. 有可能会创建出意料之外的节点.__

由于html的parser非常的__“友好”__， 以至于它接受并不规范的写法，从而创建出意料之外的结构，而开发者得不到错误提示。

-----

好了，到现在为止，我们大概了解了`innerHTML`这个朝夕相处的小伙伴，接下来我们正式聊一聊模板技术，首先我们从最常见的**“String-based templating”**开始

## String-based templating

基于字符串的模板引擎最大的功劳就是把你从大量的夹带逻辑的字符串拼接中解放出来了，由于它的完全基于字符串的特性，它拥有一些无可替代的优势。

> It is essentially a way to address the need to populate an HTML view with data in a better way than having to write a big, ugly string concatenation expression.
--- cited from http://www.dehats.com/drupal/?q=node/107


__示例__

1. [mustache](http://mustache.github.io/)及其衍生: 弱逻辑
2. [Dust.js](http://linkedin.github.io/dustjs/): 强逻辑 (推荐)
3. [doT.js](olado.github.io/):  超级快

__基本原理__

<a href="http://modernweb.com/wp-content/uploads/2014/09/String-based-Template.png"><img src="http://modernweb.com/wp-content/uploads/2014/09/String-based-Template.png" alt="String-based Template" width="540" class="alignnone size-medium wp-image-3150" /></a>

如上图所示，我们发现字符串模板强依赖于`innerHTML`(渲染), 因为它的输出物就是字符串。由于这篇文章的重点不在这里，我们不会再对它们如何使用作深究。


__优点__

1. 快速的初始化时间: 很多angular的簇拥者在奚落String-based templating似乎遗漏了这一点。
2. 同构性: 完全的dom-independent，即可作为用服务器端和浏览器端(客官先不要急着搬phantomjs哈).
3. 更强大的语法支持：因为它们都是不是自建DSL就是基于JavaScript语法，Parser的灵活性与受限于HTML的Dom-based模板技术不可同日而语

__缺点__

1. 安全隐患:  见`innerHTML`
2. 性能问题：见 `innerHTML`.
3. 不够聪明:  见`innerHTML`(呵呵)，除此之外render之后数据即与view完全分离。

尽管在这几年的发展之下，由于异常激烈的竞争，基于字符串的前端模板技术变得越来越快，但是它们显然大部分都遗漏了一些问题

1. 大侠们你们没有考虑进把输出字符串加载到Dom的时间，这才是[真正瓶颈之一](https://github.com/leonidas/transparency/wiki/Defining-template-engine-performance)
2. 不在相同功能前提下的对比有意义么？

## Dom-based Template Engine

近几年，借着Angularjs的东风，Dom-based的模板技术开始大行其道，与此同时也出现了一些优秀的替代者，就我们国人而言，近的就有[@尤小右](http://weibo.com/arttechdesign)的[Vuejs](http://vuejs.org) 和 [司徒大大]()的[avalonjs](https://github.com/RubyLouvre/avalon)。看仓库就可以发现风格也是完全不同：1) 一个简洁  2)一个奔放不羁

__示例__

1. Angularjs:  都28000star了还需多说么
2. Knockout:  在此领域内，对Web前端而言是鼻祖级的


__大致流程__



<a href="http://modernweb.com/wp-content/uploads/2014/09/Dom-based-Template.png"><img class="alignnone size-medium wp-image-3143" src="http://modernweb.com/wp-content/uploads/2014/09/Dom-based-Template.png" alt="Dom-based Template" width="540" /></a>

Dom-based的模板技术事实上并没有完整的parse的过程(先抛开表达式不说)，如果你需要从一段字符串创建出一个view，你必然通过`innerHTML`来获得初始Dom结构. 然后引擎会利用`Dom API`(`attributes`, `getAttribute`, `firstChild`... etc)层级的从这个原始Dom的属性中提取指令、事件等信息，继而完成数据与View的绑定，使其"活动化"。

所以Dom-based的模板技术更像是一个数据与dom之间的**“链接”**和**“改写”**过程。

_注意，dom-based的模板技术不一定要使用`innerHTML`，比如所有模板都是写在入口页面中时, 但是此时parse过程仍然是浏览器所为。_


__优点__

1. 是活动的:  完成compile之后，data与View仍然保持联系，即你可以不依赖与手动操作`Dom API`来更新View
2. 是**运行时**高效的: 可以实现局部更新
3. 


__缺点__

1. 部分请见innerHTML
2. Link过程对Dom强依赖：
3. 信息承载于属性中，这个其实是不必要和冗余的的。
	部分框架在读取属性后会通过诸如`removeAttribute`的方式移除它们，其实这个不一定必要，而且其实并无解决它们Dom强依赖的特性
4. 大

## Living Template Engine



String-based 和 Dom-based的模板技术都或多或少的依赖与`innerHTML`, 它们的区别是一个是主要是为了__Rendering__ 一个是为了 __Parsing__ 提取信息


>  所以为什么不结合它们两者来完全移除对`innerHTML`的依赖呢？

我们可以利用一个如字符串模板的自定义DSL来描述结构并在Parse后承载信息(AST)，并在Compile时候,利用这些结构和`Dom API`来完成View的组装，在组装过程中，我们同样可以引入Dom-based模板技术的诸如`Directive`等优良的种子。

事实上，值得庆幸的是，已经有几个现实例子在这么做了。

__例子__

1. [htmlbar](https://github.com/tildeio/htmlbars):  运行在[handlebar]()之后的二次编译
2. [ractivejs](https://github.com/ractivejs/ractive):  独立
3. [Regularjs](https://github.com/regularjs/regular) 独立,  __本文作者结精之一__


__基本原理__

<img class="alignnone size-medium wp-image-3144" src="http://modernweb.com/wp-content/uploads/2014/09/Living-Template.png" alt="Living Template" width="540" />

就如图中所示，parse和compile的过程分别类似于String-based 模板技术 和 Dom-based模板技术，从而可以结合它们的优势。

下面来完整讲述下这两个过程

### 1 . Parsing

首先我们使用一个内建DSL来解析模板字符串并输出AST。

例如，在[regularjs](https://github.com/regularjs/regular)中，下面这段简单的模板字符串

```html
<button {{#if !isLogin}} on-click={{this.login()}} {{/if}}>
  {{isLogin? 'Login': 'Wellcome'}}
</button>'
```

会被解析为以下这段数据结构

```javascript
[
  {
    "type": "element",
    "tag": "button",
    "attrs": [
      {
        "type": "if",
        "test": {
          "type": "expression",
          "body": "(!_d_['isLogin'])",
          "constant": false,
          "setbody": false
        },
        "consequent": [
          [
            {
              "type": "attribute",
              "name": "on-click",
              "value": {
                "type": "expression",
                "body": "_c_['login']()",
                "constant": false,
                "setbody": false
              }
            }
          ]
        ],
        "alternate": []
      }
    ],
    "children": [
      {
        "type": "expression",
        "body": "_d_['isLogin']?'Login':'Wellcome'",
        "constant": false,
        "setbody": false
      }
    ]
  }
]

```

这个过程有以下特点

1. 灵活强大的语法，因为它与基于字符串的模板一般，DSL是自治的，完全不依赖与html，你可以想像下dom-based的模板的那些语法相关的指令，**事实上它们甚至无法表达上述那段简单的模板的逻辑。**
2. Living模板技术需要同时处理`dsl元素` 与 `xml元素`来实现最终视图层的活动性，即它们是**dom-aware**的，而在字符串模板中其实`xml元素`完全可以无需关心，它们被统一视为`文本元素`。

  
### 2 Compiler

结合特定的数据模型(在regularjs中，是一个裸数据)， 模板引擎层级游历AST并递归生成Dom节点(不会涉及到`innerHTML`). 与此同时，指令、事件和插值等binder也同时完成了绑定，使得最终产生的Dom是与Model相维系的，即是**活动的**. 

事实上，Living template的compile过程相对与Dom-based的模板技术更加纯粹, 因为它完全的依照AST生成，而不是在原Dom上的改写。

以上面的模板代码的一个插值为例:`{{isLogin? 'Login': 'Wellcome'}}`。一旦regularjs的引擎遇到这段模板与代表的语法元素节点，会进入如下函数处理

```javascript
// some sourcecode from regularjs
walkers.expression = function(ast){
  var node = document.createTextNode("");
  this.$watch(ast, function(newval){
    dom.text(node, "" + (newval == null? "": String(newval)));
  })
  return node;
}

```
正如我们所见， 归功于`$watch`函数，一旦表达式发生改变，文本节点也会随之改变，这一切其实与angularjs并无两样(事实上regularjs同样也是基于脏检查)

与Dom-based 模板技术利用Dom节点承载信息所不同的是，它的中间产物AST 承载了所有Compile过程中需要的信息(语句, 指令, 属性...等等). 这带来几个好处

  1. **轻量级**, 在Dom中进行读写操作是低效的.
  2. **可重用的**. 
  3. **可序列化** , 你可以在本地或服务器端预处理这个过程。




### living template's 近亲 —— React

React当然也可以称之为一种模板解决方案，它同样也巧妙规避了`innerHTML`，不过却使用的是截然不同的策略：react使用一种`virtual dom` 的技术，它也同样基于脏检查，不过与众不同的是，它的脏检查发生在view层面，即发生在virtual dom上，从而可以以较小的开销来实现局部更新。


__Example__

```
var MyComponent = React.createClass({
 render: function() {
   if (this.props.first) {
     return <div className="first"><span>A Span</span></div>;
   } else {
     return <div className="second"><p>A Paragraph</p></div>;
   }
 }
});

```

同样的逻辑使用regularjs描述

```html
{{#if first}}
  <div className="first"><span>A Span</span></div>
{{#else}}
  <div className="second"><p>A Paragraph</p></div>;
{{/if}}

```


__仁者见仁智者见智__, 反正我倾向于使用模板来描述结构，而不是杂糅Virtual dom和js语句。你呢？



## 一个全面的对照表



| Contrast /Solutions  |  String-based templating | Dom-based templating  | Living templating|
| -------------|-------------             | ---------| ------------- |
|例子       |  Mustache,Dustjs|Angularjs, Vuejs  |Regularjs 、Ractivejs、htmlbars||Usage| | | |
|语法| ♦♦♦ | ♦♦♦  | ♦♦♦ |
|活动性| X | ♦♦♦ | ♦♦♦|
|性能| 初始: ♦♦♦<br/> 更新: ♦ | 初始: ♦ <br/> 更新: ♦♦♦ | 初始: ♦ <br/> 更新: ♦♦♦   |
|安全性| ♦ | ♦♦ | ♦♦♦♦♦ |
|Dom 无关| ♦♦♦♦♦ | X | ♦♦ |
|SVG support(*1)| X | ♦♦ | ♦♦♦ |






{%endraw%}



