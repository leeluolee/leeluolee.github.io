title:  数据驱动视图(MDV)技术面面观
tags: []

---

__这个面面观系列共有三篇，此文是第二篇。__

实际上这篇文章是上篇[前端模板技术面面观]()的自然衍生，我们探究的是随knockout等框架兴起的数据驱动视图的技术。


__与上篇文章一样，这篇文章的出发点也是具有客观性的原理层面__, 即我们可以抛开一些主观原因，比如: 维护者的个人水平和勤勉态度等等。

## 前言

数据驱动视图即__model driven view__, 它代表着一种由数据状态驱动UI视图的技术，，就web开发而言即Dom，取代零散

###__一个有点真实的例子__

有一天，有一位用户点击了__锤子手机__的预约按钮，两个前端狗需要处理类似的页面。

__前端狗1__以mdv的角度，它需要做的业务逻辑是

```javascript
chuizi.count += 3;
```

__前端狗2__是jQuery的死忠，它眼中的业务逻辑是

```javascript
var count = $('#topsale .chuizi').text(); 
$('#topsale .chuizi').text( +count + 3 );// 排行榜
$('#topsale-month .chuizi').text( +count + 3 );// 月排行榜
...
```

下午__运营哥__发现预约按钮下需要加上数字

__前端狗1__: "哦" -  `<span>{{count}}<span>` 

__前端狗2__: `<span class='chuizi'></span>`

```javascript
$('#book .chuizi').text( +count + 3 );// 预约数
```

第二天，眼疾手快的__运营哥__发现竟然有少量不明群众的观众发现了锤子预约数是3倍数，赶忙吩咐前端狗__修复__问题。

前端狗1
```javascript
chuizi.count += 1
```

前端狗2
```javascript
var count = $('#topsale-year .chuizi').text(); 

$('#topsale-year .chuizi').text( count + 1 );// 年排行榜
$('#topsale-month .chuizi').text( count + 1 );// 月排行榜
```

第三天。。。

不要纠正我为什么前端狗二要在每一个，我

在这里我们要谈的是：

1. 基于脏检查
2. 基于setter和getter

##存取器

存取器

1. 表达式肯定没有

##关于react

首先react当然是属于脏检查，各位有异议的除了看官网和人云亦云之外要学会主观判断哈。

区别至于angularjs，regularjs这类的数据层脏检查，react的脏检查发生在view抽象层——virtual dom. 



## 脏检查VS存取器

事实上，作者也无法得出谁优谁劣的结论