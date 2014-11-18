title: CSS预处理器使用的一点点心得(此文未完成)
date: 2013-8-4 16:39:51
tags: ['mcss', 'css']

---

软件开发领域，很多工具都是双刃剑，CSS预处理器也是如此，此文会从MCSS作者的使用角度提供一些使用MCSS的基本建议，帮助规避一些风险。

_前面章节的大部分建议对其它预处理器仍然有效_

<!--more-->

## 本文目录

1. [参数控制](#var)
2. [了解输入对输出的影响](#outport)
3. [嵌套层级不应过深](#nest)
4. [mixin函数还是@extend?](#mixin)
5. [合理利用@abstract和@import](#abstract)
6. [善加利用mcss的闭包特性](#closure)
7. [了解@for循环可以做什么](#for)
8. [关于格式的那点事](#format)




--------------

<a name='var'></a>
## 参数

1 参数名应该达意，尽量不要用缩写。

  例如设置清除浮动的mixin函数，推荐使用`$clearfix`而不是`$cb`，需要缩短selector的话可以在设置类名时进行精简

  比如整站的主色调为`red`, `$primary` 要优于


2 每一个工程都应该有个类似 __`variable.mcss`__ 的文件统一管理参数

  ```

  ```
3 对于可能的客户配置项的选项使用`?=`赋值符号




<a name='var'></a>
## 首先你要清楚MCSS的每个特性对于输出的css的影响


<a name='var'></a>
## 嵌套层级不要太深(n <= 3)

虽然嵌套的一种功能就是显示的表现出层级关系, 但是仍然不应该事无巨细的去描述。这其中有几点原因

1. 代码不美观，显得啰嗦


__几点建议__

1.




<a name='var'></a>
## 灵活使用mixin函数 以及 @extend

首先你要理解`@extend` 和 `mixin函数`的区别, `@extend`意味当前ruleset继承于指定的ruleset(由选择器唯一指定)，而`mixin函`

1. `@extend` 显示继承某个ruleset无法传入参数, 而`mixin函数`可以传入参数

2. `mixin函数`的后果时，函数调用后产生代码会混入原ruleset, 而`@extend`仅仅只是追加当前的`selectorlist`到被`@extend`的ruleset，如

3. `mixin`在一个变量会造成多个影响时很有用，比如

4. 没有规定无法同时使用`@extend`和`mixin函数`

5.


<a name='var'></a>
## 合理利用好@abstract、@import



<a name='var'></a>
## 在封装你的基础库时善加利用MCSS的闭包特性

MCSS的闭包特性并不是无用的语法糖


__举几个例子__



## 关于格式


------------------

## 结尾

其实mcss主要针对的用户群就是LESS的重度拥护者，而且了解了LESS相当于对CSS Preprocessor有了一定的了解，并且可能在应用中有了信息需求
