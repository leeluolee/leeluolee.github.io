title: CSS预处理器初识
date:  2013-8-1 22:49:03
tags: ['mcss', 'css']

---

## 前言

这些年，从淘宝的工程师翻译LESS首页以及bootstrap带动的LESS热波及到了国内，预处理器开始渐渐被一些工程师所接受。 [@w3cplus](http://www.w3cplus.com/sassguide/comments.html) 最近也开了一个预处理器专版,主要是针对SASS，版面不错(相较于SASS主页)。同时豆瓣也利用stylus封装了一个简单css framework, 目前三款预处理器算是都有了一定的受众(当然90%应该都是LESS用户)。

此文会简单进行对预处理器的介绍并进行几款预处理器的简单对比(主要是加入了MCSS,类似的对比文是在太多了)。

<!-- more -->

--------------------------------------------------------------

## CSS的不足 —— 抽象能力

CSS是一个近乎完美且富有表现力的语言（抛开永无止境的标准化进程不谈）, 但是仍有很多开发者诟病它的不足，总结下大概有以下几点

1. 难以维护
2. 无有效做到DRY(Dont repeat yourself)
3. 难以真正将既有经验积淀成实际的代码
4. 缺乏逻辑控制的能力
5. ..............

这以上其实都可以归结为 __抽象能力__ 的不足，这个所谓的抽象能力似乎听起来本身就很抽象。我们可以在维基对它的词条上一窥究竟: 

>Abstraction tries to reduce and factor out details so that the programmer can focus on a few concepts at a time.

是的， __抽离细节，关注更少__ 这种抽离无所谓大小， 事实上我们在编程中充斥不断的抽象提取的过程: 小到一个变量参数、一个函数， 大到一个类、模块和基于以上的架构模式。

并不仅限于普通编程语言，CSS也有其抽象能力，那就它的名字的含义—— __层叠样式__ ，具体到实际开发时，我们做的有可能是定义一个类名，进行增量的功能添加。这几乎也是CSS仅有的抽象能力，也很容易引起滥用，比如设置一个`display:none`样式的类，然后一个个添加到html中，这其实是应该尽量避免的，过分依赖会导致效率低效和难以维护。

以编程语言的眼光来看，CSS确实抽象能力不足，它也在不断发展（从几套css variable的草案进化就可以一窥端倪）来改善这个问题。但是它实际上并不是一般意义上的编程语言，它有其自身的切实关注点。 这时CSS Preprocessor来解围了。

##初识CSS预处理器

我们先首先来回顾下CSS预处理这个领域的发展。再来看看它们帮我们解决了什么问题。

### 预处理的'悠久'历史

最原始的CSS预处理器的形式可以追溯到利用服务器端模板语言(如php)通过字符插值拼接输出css，最后慢慢演变为类似SASS的css预处理语言, 不过当时的预处理器偏重语言特性多一点, 与CSS的语法偏离较大(可以参考SASS的语法)。 

之后LESS出现, LESS基于CSS语法的超集，带来的好处有很多，这个特性也被Sass社区所接纳。 随着SASS的另一个壳SCSS的产生，CSS-Preprocessor开始统一向着正确轨道靠拢——即基于CSS语法

而11年出现的TJ大神的Stylus似乎要将社区带回最初SASS的位置(虽然它仍然可以支持原生css的写法), 依赖缩进和约定去除多余的符号(比如`;`、`:`、`{`);

下一节我会仔细探讨下当前市面上的各个预处理

## 市面上的CSS预处理们

SASS、LESS、Stylus是非常优秀的开源产品, 每一个的产生都推动的社区的前进, 作为一个后来者，MCSS也是广泛参考Sass、Less社区讨论的衍生产物。MCSS也希望自己的理念可以解决前辈们没有解决或未关注到的问题。


### 1. [SASS](http://sass-lang.com/) —— 最鲁棒和冗杂的预处理器鼻祖
SASS早在07年就有雏形产生，伴随着Rails社区不断发展壮大，到目前为止，仍然在CSS预处理器占有度中占据着半壁江山。google甚至直接在Chrome上对其进行实验性质的支持。
但同时SASS也是有几个比较严重的问题的

1. 基于Ruby 

  无法更有效的贴近目前实际的前端开发者，之前Sass的壮大主要是由于Rails这个一体化开发框架带来的效应，很多Web开发者承担着CSS的开发任务，而目前前端开发领域越来越复杂，更多的前端开发者有能力和需求去接受css预处理器。

2. 语法有些冗杂

  这个一方面是由于Sass悠久的 __历史包袱__引起的, 另一方面也是对功能缺乏取舍的后果。比较典型的例子就是难以捉摸的`@extend`规则和那些循环语法们

从LESS、SASS的社区活跃度对比来看，这些年LESS要比SASS发展的要好得多，但是SASS比LESS在后续的扩展上要有更大的自由度——依赖自定义`@At-rule`，而这个在LESS中被占用成了变量声明，LESS只能在选择器上做文章了。

### 2. [LESS](http://lesscss.org/) —— 最火和最像CSS的预处理器

目前版本的LESS是基于Nodejs构建，并且可以在浏览器中运行（在实际开发中请忘记这个功能），目前LESS在github上的start数达到了9000，作者遇到的同事用过预处理器的也几乎都是LESS的支持者。

__LESS的火的原因是什么?__

1. 拥有50000+ star数的 __bootstrap__
 
  有个大平台社区的推动，想不火都难啊。有多少人是因为bootstrap关注到LESS的？ 请举个手。反正我是的

2. 最像CSS
  
  这个像指的是词法上最接近，热爱LESS的人们忘记CSS对于`@atrule`的定义吧，因为LESS使用了这个词法对象作为参数了，成就了简洁的美誉但是却影响了后续的功能扩展。这同时也带来了其它好处，比如几乎不需要去写对应的语法高亮了，开启css的就行，并且更容易被前端开发所接受。

3. 突出了最常用的80%的功能
  
  众所周知，LESS晚于SASS除了语法上的不同，没有引入额外的功能，而是进行了精简，混合了`mixin`和`ruleset`虽然给后续扩展带来不足，但是确实是聪明的设计。

但是事实上，大家似乎很少注意到这点 

>LESS目前仍然没有出现过一个类似Compass这种功能层级的二级类库

LESS目前有了extend了， 似乎SASS党们无法再攻击它的这个劣势，在我看来LESS似乎是往错误的方向又走了一步，比如两个并列的Selector，你如何判断哪个是extend的？如何处理层级的extend？这些本就可以通过扩展@atrule进行解决，但是设计上走错了一步会导致后续的扩展



### 3. [Stylus](http://learnboost.github.io/stylus/) —— 让人眼前一亮的语法设计
Stylus是Node社区的大神TJ的大作， 看似更少的语法噪音(括号, 冒号，分号)，通过缩进来解释, 事实上可能带来维护的深渊，多种语法支持看似灵活，其实在团队开发中也可能带来灾难。 

综上所述，对于以上三款，我偏向仍然是SASS(SCSS)。文章的末尾 

### 4. [MCSS](https://github.com/NeteaseWD/mcss)

从SASS出现，再到LESS(LESS在功能上只是子集), 再到Stylus, 语法特性整个社区其实一直在增加，但是无论它们任何一个，其实都没有解决或关注到一些一直存在的问题: 

1. 无法控制一整个文件模块的输出

2. block不是一种值，无法传递进函数, 这让@keyframes、@media的封装成为不可能

MCSS是我最近完成的CSS预处理器，它同时也可以作为一个 __易用的CSS Parser__ , 在小范围已经使用了约两个月， 已经有了完善的测试已经文档， 目前的版本是`0.3.4` 这里不会事无巨细的介绍太多它的功能点。有兴趣的可以参考



------------------------------------------

## CSS预处理器的共性

_共性是一种类似最佳实践的存在，被各个预处理器社区广泛接受和实现的特性。这里我会以Sass、Less、Mcss三种预处理器进行短暂介绍，以及帮助我们解决什么问题_

#### 1. 参数、赋值

参数是一种最基本的抽象, 

```

//less
@primary: #ccc;

//scss
$primary: #ccc;

//mcss
$primary = #ccc;

```








## CSS预处理的几个误区 


## CSS预处理的几个良好实践

### 1. 嵌套尽量不要超过三层

有人可能会疑问: _使用嵌套层级不就是为了更好的表明

如果你发现你的源文件大范围的超过三层, 或许你应该重构你的html了

### 2. 


## 个人实际使用中的预处理器




## 相关资源

1. [Why Abstraction Can Improve Your CSS](http://www.vanseodesign.com/css/abstraction/)
2. [why stylesheet abstraction matters](http://chriseppstein.github.io/blog/2009/09/20/why-stylesheet-abstraction-matters/)
3. [CSS Syntax](http://dev.w3.org/csswg/css3-syntax/#parsing)
4. [几个预处理器的流行度对比 2012数据](http://css-tricks.com/poll-results-popularity-of-css-preprocessors/)
5. [来自mdn的CSS syntax中文解释 短小精悍](https://developer.mozilla.org/zh-CN/docs/CSS/Syntax)
6. [musing on preprocessing](http://css-tricks.com/musings-on-preprocessing/)







## 后续

1. 后续我会仔细阐述mcss的语法以及功能特性以及这个特性所要解决的问题