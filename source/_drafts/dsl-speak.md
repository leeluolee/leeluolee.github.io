
#P1

大家好，今天我要分享的题目是 前端DSL实践。
 在加入网易的2年多时间里，我持续地在接触关于DSL相关的技术，对于DSL领域有了一些不算深刻的认识。对于我个人而言，DSL已经是个人的非常重要的工具，
 且已经获益匪浅，今天我主要是分享下这一路下来的所见所想。

这个标题事实上有点大，事实上以目前个人的演讲能力是hold不住超过1个半小时的演讲的，所以这次演讲主要还是
会放在普及DSL在前端的应用上


@切换

话说了那么多，那么问题来了？什么是DSL？

Martin Flower　曾经这样定义过:
针对某一领域，具有受限表达性的一种计算机程序设计语言


[[提问：大家知道这个是什么人吗？　不重要啊，反正大家肯定都觉得它很牛逼对吧，效果达到了
事实上，此人真的很牛逼，在ＤＳＬ领域有多篇Paper，这本书也是它写的]]


dad的骄傲离开
那么问题又来了？能更具体点么

我先不尝试乏味的去解释这句话的意思，我先转为问大家两个问题


@切换


在座的有用过jQuery吗？好用吗

```javascript

$("div.first")
  .slideUp(300).delay(800).fadeIn(400)
  .find('.close').fade();

```

如果的js api使代码量相同,　表达能力上也会大为不同。稍后我会为大家揭开jQuery被大众
接受的原因



座的有使用过模板引擎吗？



我相信听完我这两个疑问，大家的问题又来了，这两个例子有任何可比性么？

事实事实上它们都属于DSL的范畴，但是是隶属于截然不同的分支，后续的分享会慢慢梳理。



# P3 : 今天的分享范围是


1. ＤＳＬ是什么
2. 为什么要ＤＳＬ
2. ＤＳＬ的实践
3. ＱＡ

在开始之前，我们先来熟悉几个名词。

1. 抽象: 抽象可以说是我们程序员的本能，我们习惯将熟知的部分封装起来，而关注更上层的业务逻辑。定义一个变量、函数，模块，Class，甚至架构都称之为抽象，而构建DSL是一种更高层次的抽象，它跳脱（或部分跳脱）了语言本身的表达能力
2. 一般接口(command-query-interface):



## DSL的定义
过了之前的例子之后，我们重新来回顾下这句定义,

__针对某一领域__，__具有受限表达性__ 的一种 __计算机程序设计__ __语言__

计算机程序设计语言：这个是根本，否则和一些所谓的行业术语就无法进行区分，
你和服务生的订餐术语也能成为ＤＳＬ，那ＤＳＬ也太博爱了。这个是与非计算机领域的一次区分

语言性：它必须具有语言性，即连贯的表达能力，语言性很多是为了区分接口与内部ＤＳＬ的区别

受限表达性：这个是为了与通用编程语言GPPL做区分，DSL只有当前领域所需的的最小集


针对领域：只针对具体的领域，其实这是首先表达性的必然结果


计算机或软件领域有很多有定义但是模棱两可的概念和术语，例如MVC、例如设计模式。
往往思考角度不同.　概念归属就不同

我们先以语言发展的历史来查看领域专用语言



低级语言: 越低级的语言对于程序员的要求越高，可控性也更强

![](http://h.hiphotos.baidu.com/baike/c0%3Dbaike116%2C5%2C5%2C116%2C38%3Bt%3Dgif/sign=e26853be02087bf469e15fbb93ba3c49/023b5bb5c9ea15cec92e8cfcb6003af33b87b207.jpg)

__代码应该是优先让人理解阅读，顺便让机器运行__,　汇编和机器语言显然无法满足这点

高级语言
  ```coffeescript

  mood = greatlyImproved if singing

  if happy and knowsIt
    clapsHands()
    chaChaCha()
  else
    showIt()

  eat food for food in ['toast', 'cheese', 'wine']

  foods = ['broccoli', 'spinach', 'chocolate']
  eat food for food in foods when food isnt 'chocolate'

  ```

  高级语言引入了很多趋近于自然语言的词语和句子结构，并且很多高级语言都是ＯＯ语言，它可以抽象现实的
  实体反映在具体代码中，但应对与具体领域来讲，高级语言仍然不足于表现转有领域的具体问题，

  就我们开头提到的模板引擎来说，在出现模板引擎之前或偷懒没有使用模板引擎时。
  相信你可能会写出这样的代码

  ```javascript
  var user;
  var html = "<div "
  if(user.isLogin) {
    html += "class='z-login'>"
  } else{
    html += ">"
  }
  html += user.name;
  html += "</div>"
  return html;
  ```
  1. 可读性大差
  2. 表现与逻辑杂糅在代码中

  ```mustache
  <div{#isLogin} class='z-login'{/isLogin}>{name}<div>
  ```

  模板等会说到是属于最典型的外部ＤＳＬ. 模板本身是一种抽象，即对组装
  字符串的业务逻辑的抽象。它将简化了复杂度(或将复杂度隐藏在了ＤＳＬ本身的实现上)


DSL就是为了更高效的开发效率和沟通成本而出现的概念。



DSL分为几种类型: 1) 内部ＤＳＬ　2) 外部ＤＳＬ　3)语言工作平台

这里我们只会涉及到前两种.


## DSL的

无论内部都是完成表现　-> 内部语义模型的映射转化

## 外部ＤＳＬ

自定义语法，各个宿主环境解析这些DSL进行解析或直接执行和翻译。外部DSL会涉及到基础领域的编译原理的知识，这一环是绕不过去的，但是呢，很浅不必担心，由于领域受限性，它的复杂度相较于实现一个通用编程语言而言要低得多。如果在座的是计算机或软件专业，把这里抓起来应该是快的,　对于没有经验的，我推荐一门书籍，这本书带我把凌乱的知识体系整理了起来，[编程语言实现模式](http://book.douban.com/subject/10482195/). 这本书从头到尾充斥的核心理念就是够用就好，与我个人的极简主义简直就是不谋而合，相信也会适合大家

外部ＤＳＬ的输入通常是字符串。通常

外部DSL和GPPL的边界：
1. 受限表达性：正则表达式，DSL肯定不是图灵完备的。
显然外部DSL与通用编程语言的边界是很模糊的



其实对于很简单的复杂度的外部ＤＳＬ,
1. 分隔符指导解析: 适合语法简单的场景
映射
![](http://knockoutjs.com/img/homepage-example.png)

knockout 显然是一个典型的分隔符指导的例子。它的所有的binder都集中在data-bind
而不是angularjs一样 分离在各个ng中. 分隔符指导的翻译只需要简单的split+正则就可以
帮助我们解决问题。

然后一旦语法变得复杂，分割指导的方案将不再凑效

2. 语法指导解析，BNF, 这里我不再想讨论BNF是什么。简而言之他是描述一个语言的
对于不是很了解的同学，或者和我一样不是计算机相关专业的同学，我这里
![dada](http://img5.douban.com/lpic/s7661036.jpg)

语法指导解析即根据文法规则(BNF)按从文本解析为AST.




词法解析 -> 语法解析 -> （AST树改写:　执行改写） ->  翻译



##内部ＤＳＬ

寄生于当前宿主环境的DSL，语法功能上有受限性，，在解决某类领域问题时形成一定的书写风格（大部分情况都是接口风格），内部DSL强烈的依赖于宿主语言本身的表达能力.


内部DSL的一个同义词是流畅接口, 因为流畅接口更容易实现类似自然语言的描述方式，这点
更容易被包括我们程序员本身在内的领域专家接受。

但是它们不能完全等同，我们可以参考下另一个ＤＳＬ

```javascript
def ("Person") ({
    init: function(name){
        this.name = name;
    },

    speak: function(text){
        alert(text || "Hi, my name is " + this.name);
    }
});

def ("Ninja") << Person ({
    init: function(name){
        this._super();
    },

    kick: function(){
        this.speak("I kick u!");
    }
});

var nj = new Ninja("ninja");

```

很显然，一眼你就可以看出这是　类似与Ruby的类继承方式，但是实际上它是合法的javascript


回答流畅接口的定义。

内部ＤＳＬ容易产生混淆的就是常规接口， 边界条件是语言性。在使用内部DSL时，它给人的感觉是一个有连续关系的整句，而不是一系列命令的集合。

1.until(5).need('work'). 由于这种内部DSl为了表达性，通常会加入与程序本身无关的辅助词汇(until, needdeng1)，这点在常规的一般API的设计准则里是不被认可的。


简单的例子就是单元测试中的断言库，　常用的有Qunit, 这也是jQuery的官方测试库.　不过这里我们会选择噪音更小的断言库Chaijs




```javascript
chai.should();

foo.should.be.a('string');
foo.should.equal('bar');
foo.should.have.length(3);
tea.should.have.property('flavors')
  .with.length(3);


```
它同时支持另一种ＢＤＤ风格的写法　expect

```javascript
var expect = chai.expect;

expect(foo).to.be.a('string');
expect(foo).to.equal('bar');
expect(foo).to.have.length(3);
expect(tea).to.have.property('flavors')
  .with.length(3);

```

如果不用这类断言库，我们可以解决这个问题吗？当然可以

```javascript

if(typeof foo !== "string") throw "foo is not a string";
if(foo !== "bar") throw "foo is not equal with 'bar'";
if(foo.length !== 3) throw "foo's length is not equal with 3";
if(foo.hasOwnProperty("flavors") && foo.flavors.length === 3) throw "xx error";

```


大家可以发现，虽然纯粹都是javascript，但是在可读性上，无论你是否是程序员，都是应该第一直觉是前者更易读

配合Mocha等测试框架，可以输出非常易读的reporter

![](http://mochajs.org/images/reporter-html.png)

源码和报表简直就是一样一样的。这个就是ＤＳＬ带来的超能力




接下来我们来认真聊一聊内部DSL的实现方案.　之前我们提到了，流畅接口是基于函数、方法的语义化封装，


函数使用模式

1. 方法级联(链式接口)
2. 函数函数序列()
3. 嵌套函数

这里原书有个非常浅显的例子，我这里直接挪过来进行使用了




宿主语言会影响到，作为内部DSL的可能性和表达能力
对于可能性，比如java和javascript,不支持动态接受能力的语言实现不了类似的能力

而对于表达能力.　做个简单对比。在座的有没有使用过react的同学，react是目前脱颖而出的新一代组件级框架，利用它你可以这样来定义你的ui组件


```javascript
var HelloMessage = React.createClass({
  render: function() {
    return <div>Hello {this.props.name}</div>;
  }
});

```

这里没有任何魔法在里面，ｘｍｌ当然不能，这个其实是jsx写的，一种可以在javascript中插入xml的语言。　那么问题又来了，如果我不想引入jsx这一层。我该怎么写？结论是反人类的

以以下模板为例

```html
<div className="MarkdownEditor">
  <h3 title='title'>Input</h3>
  <textarea onKeyUp={this.handleKeyUp} ref="textarea">
    {this.state.value}
  </textarea>
</div>

```

这是典型的外部DSL的写法，这个与常规的模板写法没有太大出入


```
var d = React.DOM;
d.div({className: 'MarkdownEditor'}, [
  d.h3({className: "title"}, 'Input'),
  d.textarea({onKeyUp: this.handleKeyUp, ref: 'textarea'},
    this.state.value
  )
])
```

这些`div`、`h3`其实就是完成了之前所述的表达式生成器的功能，这个直接产生的结果是
生成了virtual dom.

这是典型的内部dsl的写法，而且是这种函数的写法其实是我们之前提到过的？嵌套函数
这个例子也验证了我之前提到的，取决于，拥有确定的语言模型是非常重要的。其实做的仅仅


诚实的说，但这结构一复杂还能忍吗？但是假如我们使用coffeescript来重构这段代码

```coffeescript无异

{div, h3, textarea} = React.DOM

div className: 'MarkdownEditor',
  h3 title: 'title', 'Input'
  textarea onKeyUp: @handleKeyUp, ref: 'textarea',
    @state.value

```

突然就变得容易接受了呢。这就是语法噪音带来的巨大影响，将会极大的影响语言的表达能力

coffeescript和ruby等语言都是基于缩进的，直接的结果就是语法噪音小，是内部ＤＳＬ的天然温床，以coffeescript为例, 在构建工具上有cake、模板上有coffeekup都是小众但是簇拥者众多的内部ＤＳＬ的开源作品







# 今天我们来谈谈前端DSL


对于软件开发领域来讲, DSL似乎是并不那么常用，前端尤甚，特别是我们常规的业务开发中，事实上，我们每天都在接触这些，比如XML、make、测试套件、正则表达式、SQL。
假设没有这些，单纯靠层层抽象我们是否可以得到, 外部DSL的还有一个好处是，与执行宿主无关，比如正则表达式，
(当然各个语言的实现不同，比如JS不支持前向断言，这其实在DSL)



# DSL的定义






#　说了这么多有没有感觉到我什么都没有说？　这也不是那也不是，就不能给个１００％准确的
定义？　事实上在知道ＤＳＬ这个词汇之前我已经几乎完成了mcss的开，去涉及下这个领域，当你碰到问题是，ＤＳＬ可能会成为你脱颖而出的杀手锏
