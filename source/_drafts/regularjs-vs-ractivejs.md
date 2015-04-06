title: Regularjs的框架——Ractivejs篇
date: 2015-02-03 10:32:51
tags: [regularjs, template]
categories:
---

这里我们不再纠结框架和库

框架和库的本质区别是控制反转， 在。

而在组件对外使用时， 他们又显然都是库级别的存在

刚做完一门视频培训课程（这里打个广告），里面涉及到一个章节: 框架选型.

硬着头皮去，

以前找一个技术方案不易， 现在找一个技术方案仍然不易. 由于模板引擎，更使
被造的。

我能做的仅仅只是
，做开源从本质上市为了什么？ 自私的讲是图名， 被关注的感觉，与明星走
在聚光灯并无二样。往无私讲， 也希望自己确能为， 开源不是你把源代码扔上去就算
开源。

regularjs会一直， 因为Living template的本质，保护regularjs不会受到
未来custom-element冲突的影响。

将dirty-check的，

本系列会有三个部分. Ractivejs, Angularjs, Reactjs.

这也是 Regularjs初始放出来的三个噱头  Regularjs = R

库/框架一句话就是IOC.

这么多的前端框架

> 古人云: “酒香不怕巷子深”

现在这句话行不通了， 我深刻的体会到了自己的微博从平均400的阅读量， 到被大牛一转变上万的阅读， 这个社会目前的信息成本越来越低， 小人物必须为框架争取话语权.


## Regularjs  VS Ractivejs

Regularjs与Ractivejs几乎是目前唯二的机遇Living Template的选型。

所以如果你认同了Living Template的这种方案， 我们就可以把关注度放在, 对于两款框架，他们的可能性是相同的。

区别就是设计:

设计的关键是:  一致性、可扩展性

> 这两款框架，我们选择谁？

做regularjs老大的明确需求: 做一个模板引擎，
但是数据可以发生变化，展现也随着，但是要类似字符串的体验。

所以rgl目前的版本几乎就是我们的服务器模板。顺其自然的，包了层组件的外衣.



按我之前的分解，

## 内容前瞻

1. regularjs是什么？
2. 典型框架之间的取舍:
    这里会解答几个可能的疑惑
    1. 为什么当初regularjs使用了: reactjs(ractivejs) + angularjs 的噱头.
    2. 这三个框架的用户如何在regularjs找到熟悉的概念
3. 当之无愧的Next-Generation的框架设计
4. 为什么推荐你使用regularjs


## regularjs是什么？

作为去年才出现的新生框架， regularjs目前在网易内部的使用其实超过了本人的预期， 效果也相当不错， 或许说是数据驱动+组件化带来的结果. 可以先放个简单的例子




## 典型框架全面对比

这里会挑国人较为熟悉的angularjs, reactjs， vuejs作为对比对象， 再加一个regularjs同属living templating技术的ractivejs.

### 为什么取名regularjs?

不得不说取名regularjs是个彻头彻尾的败笔, 毫无疑问

1. reactivejs, reactjs, ractive... 太多看起来类似的取名了
2. 刚推出时， 搜索regularjs出来的基本都是关于正则表达式的
3. 由于angularjs的火爆， google搜索仍然会被纠正为 "您要找得是不是angularjs"

但是事已至此， 我还是会说下， 当初为何会取这个名字. 其实在13年在脑中就有雏形要做这个东西.

1. 是想粘angularjs的


但是可以肯定的是:

> 如果你仍然在angularjs, reactjs, ractivejs中困惑， 不知该如何做选择， 选择regularjs试试， 我相信它是个趁手的选择.


其实我本人不喜欢无意义的用一些浮夸的Slogan来吸引眼球， 我先来简单由下一代框架为引子，谈谈我对于未来框架的看法，以及regularjs做的一些工作以及未来会做的工作。

目前从微博或者邮件里反馈的消息来看， 实际上用户的提的问题还非常基础， 事实上regularjs目前已经走的很远。

目前下一代(Next-Generation), 我们可以从源头开始讲， 下一代的框架应该满足什么要求？


1. Ractive: 同为living template, 用户众多
2. Angularjs: 同为脏检查，业界的王者.
3. Reactjs:
4. Vuejs:


你可以想象成Regularjs是， 而且跟2.0的angularjs其实达到了某种程度的契合.  2.0的angularjs的主题也是组件化.  不过regularjs更彻底

当你的代码量和工作到了一定程度时， 你会发现， 实际上你花于代码实现的时间会越来越短， 几乎你看到一个特性， 可以在瞬间想象出它的实现方法。 所以


## React的component view

我们先来看下regularjs的Tabs组件的写法



## 下一代框架需要满足的条件

regularjs既然不依赖于， 比如 你没有注册(pager 这个组件), 就不会在 , 虽然regularjs不推崇将所有， 但是由于内部流程的一致性， 事实上你要纯粹的component的方式来使用 也是没有问题的. 例如这个tab组件


### 对未来的友好度

Web component的， 一切目前基于dom， 但是


### 接口优秀、充分， 方便迁移
比如stateman的结合， 比如polymer的无缝集成。 你可以目前，  然后通过一个函数来完成 Regularjs的polymer迁移. 其实不是很简易组件碎片化， 多一层组件将导致多一层的通信成本，  由于本身的描述语言是常规模板， 事实上regularjs的表现能力更接近常规模板

### 轻内核

这点国产avalonjs, vuejs(半国产)


### 动画为王


### 能屈能伸


### 关键难点得突破

### 性能



## 框架对比

这里我们不再会对Backbone这类的老一代框架做比较，


##

1.


7. so many next-generation 下一代框架. 哪个才是真的？
     1. 对未来友好: 未来是未知的我们只能以
          1. 不要杂糅进模块系统，所以最好是独立的库，并提供尽可能多得exports类型
          2. 接口友好， webcomponent
     2. 库而不是框架: 使用Regularjs开发单页SPA
     3. 现在就可以用，未来仍然可以长时间占据一席之地。现在就要
     4. 需要有一个实际的线上产品，而不是绑架社区为你提供。由于SPA基本对内，目前对外可访问的并不多。
     5. 大方向正确: Living Template


Bootstrap 的 affix





与各大家的区别，虽然各个基于oo的extend的模式越来越多，但是我始终觉得regular中的oo与模板的反映是最一致的

regular设计之初也考虑了易用性和安全性的结合


ractive
声明式的事件处理
动画系统 regular提供了一个独一无二的一致的动画系统 解决了声明式动画的问题的大难题
基于纯的脏检查
更符合富模板要求的模板语法
ie6的支持，其实对ie8的支持不可能通过一个es5 shim得以解决
性能 解析规则，流程一目了然
refs取节点. 更严格的节点选取， regularjs本身没有实现类似选择器. 因为组件本身有明确的初始化和销毁生命周期， 所以你只要在，这也是更灵活的方式
其他当然还有很多 比如双向过滤器  比如更加一致的


angularjs

其实在整个观察者体系上 抛开语法的区别 应该在使用上是最接近angular的 作者在12年沪js开始关注angular  因为连digest原理
流程都是借鉴的angular 不过处理上会比angular复杂的多 比如list 处理解析流程等 regular本身不会莫名其妙的去构建出scope 每个组件没有继承关系，大家都是自制的 这就方便了测试你可能将组件本身看城是一个小型mvvm系统.

1. @(）
2. 更强大的双向过滤器支持， 帮助你实现数据的双向流
3. 计算属性支持

react
原型其实就是，不过最初的想法是用模板代替它的render函数 但是后来发现有很多问题 比如

1. 都不急于innerHTML, 对未来友好
2. 都需要手动触发 dirty-check.   (只是一个check 发生在 数据， 一个发生在virtual-dom层)
3. 事实上所有react的所谓的架构，
4. 写法上应该是最相似的一组，  区别就是 jsx 还是 自定义DSL 来描述你的模板结构

我看了下 非常巧合的是 与国内另外两个同类模板vuejs与司徒都是50k左右的内核 相交于国外动辄100+的库来讲更合适




## 事实上，随着命名的不同， 你会发现， regularjs与当初的slogan 没有任何的出入, regularjs = angularjs  + reactjs + ractivejs. 这是我理想中得框架, 又对未来的技术没有侵入性.



顺便提一下:

regularjs 目前: 50kb
ractive: 200kb
reactjs: 200kb
angularjs: 就不需要解释

至少从体量上讲， 只有regularjs满足“类库”的定义.


regularjs目前的劣势:

1. 没有一个完善的ui库， 毕竟目前就我一个人， 而且并不算是全职在维护，
2. star上
3. 暂时仍然需要兼容IE6

regularjs目前的优势:

1. 是实际线上项目正在使用的
2. da1
3. 兼容IE6:


下一步计划， 会增加大量的文章

## 未来的regularjs

1. 目前其实
2.


## 为什么我推荐你使用regularjs

1. 兼容IE6, 目前的万全选择

    对于前端工程师而言， 兼容IE6从一个体现能力的点慢慢演化为了“陈旧无能”的代名词， 前端工程师应该主动争取不兼容IE6甚至IE8， 但是往往策划们的要求都会看似很大度:“IE6/7下只要能用就好了”, 这个“只要能用”的让步其实仅仅只是css角度而言， 但就JS框架而言, 其实还是必须要全面支持。

2. 不断完善的testcase

    testcase是靠

3. 源码仍然清晰可控
    虽然不断的进行功能迭代， 目前



{#list }


##

1. 重心放在中文文档上面




## Regularjs   VS   Ractive

几乎目前市面上的Living template 只有这两款，或许ember的htmlbar也算是一款
同样也是基于mustache,  我其实有点难以理解为何对于低逻辑的模板如此情有独钟
已知

> regularjs从头到尾的宗旨就是用尽量少的代码做更多的事情。


0. 代码量，React 只是View?  Ractive只是View？ 希望大家在看官方稿
之前也能有基本的认知能力。 在，显然都是框架. 但是我始终觉得200k的库是否太大了？

1. 组件化/模块化



1. set/get  VS   dirty-check 。 由于
没有对错， 但是我要说的是。 dirty-check是一种更一致的做法，set/get比你们
Regularjs在组件化的基础上 ，最小化dirtyCheck 带来的问题
`<countdown isolate> </countdown>` 当isolate出现时， 这也是大部分时候
`@(一次性绑定)`

2. mustache  VS  rgl
请基于客观判断，去对决哪个模板的语法是设计的更安全的，更符合一致性？

{@()} 所以有一个社区，几乎，但是语言设计能力真的无法恭维
{#if xx.title} xx {/if}

我无力去吐槽

3. 节点/组件获取

regularjs与ractive都推荐在组件中，杂糅进. regularjs的一切都是为了
实现功能需求的前提下缩小最小的代码量

4. 代码量

5. 接口数量， regularjs只有10个实例方法， 对应ractive却有33个。这是证明regularjs非常弱吗？

我帮你来分解一下.

set相关
selector相关

6. 事件。
作者是Angular粉， 所以沿用， 好在你不用去判断那么多的ng-xx, 当你学会从本质去看问题，而不是人云亦云时，
技术选型就会更加安全。

为了更好的事件传递， regular也提供ractive的代理方式 on-xxx='close'.

7. 动画
regularjs的动画精简

8. 单页
目前regularjs提供了一个单页应用的房里

9. 组件

regularjs在组件上化的功夫非常多， 基本Ractive的例子都是以

new Ractive 开始， 根据， 因为扩展是全局性的。 但是Regularjs的扩展是由作用于的
无论多小的组件Regularjs都推荐封装成组件的形式.

Regularjs 支持 initialize 改写AST 从而部分修改你的组件的展现。


10. 代码量
即源码的复杂度仍然是清晰可控的。

11. 性能
当时，写玩具选择器时， 基本把所有优化点都跑了一遍。

11. 其它
双向过滤器，

# 文档

tutor 目前确实没时间去做， 当然stateman是个独立的库， ractive粉完全
可以以类似的代码为ractive封装一个

12.

1. 组件的声明方式
这个其实是目前所谓的view层组件声明的一种共识, 包括vue,ractive,reactive,ripple


2. 浏览器支持度

IE6+. 我始终

目前regularjs仍然需要支持IE6来满足公司内的需求， 但是

虽然无法做到全职维护regularjs, 但是自由的办公环境，可以允许我在工作事件
来维护这个项目， 这个相信




前提是你接受了我上篇文章看法， 你需要在ractive与regularjs中做一个选择

可笑的是regularjs根本没坐性能优化().

同样的优势




可以说，同属Living template阵营的两者拥有同样的可能，但是从设计角度来讲
是由诸多的偏向，

__事实上我并没有去实际的使用过ractive, 所有认知，仅限于文档。 所以这些对比项，或许并，或许
一切大家都应该基于自己的考量。我很希望ractive的fans能帮助我点明不妥的地方。__

下一篇文章。将会是更， 因为往往同事在使用时，实际上，regularjs的模式




## Regularjs的 —— Angularjs篇

到了关键时刻， 最近的很容易的就是Angularjs. 除了上篇文章分析的Dom-based 和 Living template的本质区别



1. 事件响应

2. 脏检查

3. binonce的支持， regularjs从产生就有， 虽然

4. 生命周期， 库与框架的争论

5. 代码量， 学习成本。

6.
