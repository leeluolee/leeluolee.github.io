title:  数据驱动视图(MDV)技术面面观
tags: []

---

__这个面面观系列共有三篇，此文是第二篇。__ 

完成这三篇之后，我们开始由浅入深的介绍regularjs的实际应用技巧。

实际上这篇文章是上篇[【前端模板技术面面观】](http://leeluolee.github.io/2014/10/10/template-engine/)的自然衍生，我们探究的是随knockout等框架兴起的数据驱动视图的技术，准确的说是它们的模型层。


__与上篇文章一样，这篇文章的出发点也是具有客观性的原理层面__, 即我们可以抛开一些主观原因，比如: 维护者的个人水平和勤勉态度等等。

## 前言

什么是数据驱动视图？ 即MDV（__model driven view__）, 它代表着一种由数据状态驱动UI视图的技术，还是不明白？没关系。

###__先来一个纯属虚构的例子 :)__

有一天，两个前端狗要在不同页面嵌入__锥子手机__的预约功能，

每当用户点击一次预约，需要处理一些业务逻辑。

__前端狗1__以mdv的角度，它需要做的业务逻辑是

```javascript

model.$watch('count', function(now, old){
	model.month += now-old;
	model.all += now-old
})
// once click
model.count += 3;
```
每当点击，它把数量__+3__(LZ数学不好). 注意到由于`$watch`的存在总销量和月销量也会增加。


__前端狗2__是jQuery的死忠，它的业务逻辑是

```javascript

$('#banner .zhuizi').text(
	+$('#banner .zhuizi').text()+3;  // 顶部订阅数 + 3
)
$('#top-all .zhuizi').text(
	+$('#order .zhuizi').text()+3;  // 总排行 + 3
)
$('#top-month .zhuizi').text(
	+$('#order .zhuiwezi').text()+3   // 月排行 + 3
)
...
```

_* 注意勿吐槽，这里不代表作者真实的应用jQuery的水平_

下午__运营哥__加了需求需要预约按钮下需要加上数字, 并移除banner的数量

__前端狗1__: "哦" -  `<span>{{count}}台<span>`移至预约按钮下.

__前端狗2__: `<span class='zhuizi'></span>台`移至预约按钮下

```javascript
$('#book .zhuizi').text( 
	+$('#book .zhuizi').text()+3
 );// 预约数

```
并删除对应js
```javascript
//删除以下代码
$('#banner .zhuizi').text(
	+$('#banner .zhuizi').text()+3;  // 顶部订阅数 + 3
)
```

第二天，眼疾手快的__运营哥__发现竟然有少量不明真相的围观群众困惑为何锥子预约数是3倍数，赶忙吩咐前端狗们__擦屁股__。

__前端狗1__: “哦”
```javascript
zhuizizi.count += 1
```

__前端狗2__
```javascript

$('#top-all .zhuizi').text(
	+$('#order .zhuizi').text()+1;  
)
$('#top-month .zhuizi').text(
	+$('#order .zhuiwezi').text()+1   
)
```

第三天。__前端狗2__ ---->  __顶包侠__ 

-----------------------

这个故事不要在乎细节，在这个不能再简单的例子里，作者想表达的是:

1. MDV的思维可以帮助(__强迫__)你抽离你的业务逻辑到唯一的数据模型上，减小维护成本。
2. MDV的开发模式可以有效的隔离你的业务模型和展现细节，让你更有效的应对产品汪的需求更改。
3. `x3`真的不是重点哈。

###现在我猜可以仔细谈谈MDV了

[Google对mdv给的解释](http://mdv.googlecode.com/svn/trunk/docs/design_intro.html)是

> A proposal for extending HTML and the DOM APIs to support a sensible separation between the UI (DOM) of a document or application and its underlying data (model). Updates to the model are reflected in the DOM and user input into the DOM is immediately assigned to the model

话从G大神里说出来似乎就觉得很有道理了，其实表达的意思就是在文章开始表达的那句。。

不过难免会与[某个具体的实现或草案https://github.com/toolkitchen/mdv](https://github.com/toolkitchen/mdv)关联起来。(作者不清楚这个template-binding草案发展成如何了，大概1年前就开始关注，几乎一段时间就一个样，实在跟不动)产生关联，其实MDV可以很抽象，本文所认为的MDV即所有可以通过数据模型驱动UI的技术。比如[regularjs](http://github.com/regularjs/regular)(广告位)，angularjs，reactjs，avalonjs，vuejs, emberjs甚至是弱到渣的Backbone的model+view的组合。


## MDV的分类

现在我们开始深入的讨论下主题了，本文会涉及到两大类mdv技术的实现，其中每个大类又有若干个有共性也有差异的子类，它们分别是：

- 存取器（Accessor）
	1. 存取Wrap型 (setter、getter)
	2. defineProperty
	
- 脏检查（Dirty Check）
	1. 广度脏检查(数据层的观察者系统)
	2. 深度脏检查(view层抽象如virtual dom)

这里先不要深究每种类型分别代表什么
跟[上篇关于模板的文章]()一样，我会从客观的基本原理和限制来阐述我的观点，避免产生不必要的鸡同鸭讲的技术争论。



##存取器Accessor


存取器主要解决的是：__通过设置中间代理实现在修改值的时直接获得通知__，因为

这里要阐述一个误区就是

请先抛开对于Backbone.Model这种远古级的setter、getter的理解，__完备表达式支持的存取器Accessor实现__难度要大于脏检查，所以市面上的存取器实现基本都是表达式不完备的，比如Vuejs、Knouckout、Avalon等

1. 表达式的依赖提取

### 共性

### 

### 值代理defineproperty


2. 天然的痛点——数组Array
3. defineProperty不支持IE9以下
	天朝的IE6可能将亡，但IE8势必会持续很长时间，这会影响到技术选型。这里不得不提一下司徒的Avalon.js利用VBscript实现了IE6下的兼容，基本原理可以参考[国外的这篇老文章]()，这里就不细究了，原理并不复杂。

值代理有个天然的痛点就是__数组Array__

__Example__

1. Avalonjs

这个，我们就直接


存取器

1. 表达式肯定没有

##关于react

首先react当然是属于脏检查，各位有异议的除了看官网和人云亦云之外要学会主观判断哈。

区别至于angularjs，regularjs这类的数据层脏检查，react的脏检查发生在view抽象层——virtual dom. 



## 脏检查VS存取器

事实上，作者也无法得出谁优谁劣的结论