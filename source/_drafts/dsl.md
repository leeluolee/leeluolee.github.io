## 这次分享 主要关注于What 与 Why ，而不是 How，依赖，分享会争取在45分钟内结束

这次分享不仅仅只是杂糅我，而是包含了作者两年来的心血。

迷思1
1. DSL不仅仅是文本

## 参考资源

1 [DSL和模型驱动开发的最佳实践(1/4)](http://www.cnblogs.com/lonely7345/archive/2010/04/08/1707076.html)
  这部分主要是说设计。

1.语言来源：
 - 对于技术性DSL: 通常是框架、类库、架构等。对于这种需求，基本就是形式化这些既有知识。
  比如模板。我们需要提取默认特定使得DSL更加好用
 - 对于业务性DSL: 要收集领域相关的知识, 这里一般提供的是工具，例如Excel等，你需要规范化这些工具使其更加好用

2.极限表现力：
 - 大部分时候一个声明式的语言足够构建你的DSL, 你要整理出影响到可能性的方案，比如mcss的函数作为值
 - 可配置 VS 定制化: 没有纯粹的定制化和配置化. 配置化的DSL功能似乎更弱，但是暴露的复杂度会更低.. 例子directive, fitler等等.
 - 表述与实现。DSL是What， 而领域模型是How.

3 符号。
  - 符号
  - 从实现者角度讲，你更关注的一般是领域模型或其代码实现。 而对用户来讲，符号是关乎到接受度的关键所在, 你需要选择最适合当前领域的表现方式，比如一般来讲类html的模板会
    比其它结构表现方式的模板更容易被html开发者接受。变现应该尽可能的简化，即使需要额外的配置或插件式实现
    对于css预处理器而言: 函数、变量、@at-rule(如extend)、嵌套、import 这种都属于基础配置，其余都是可以列为低优先级。但是如隐式调用.
    与你的使用者交叉交流是实现优秀符号门面的关键

4 图形还是文本:
  1. 类似与时间流、实体间关系、用图形显示会更明白易懂一些，比如AI的编辑器
  2. 文本编辑更容易继承到普遍的编译流中。
 [DSL和模型驱动开发的最佳实践(2/4)](http://www.cnblogs.com/lonely7345/archive/2010/04/08/1707076.html)

5 一致性
  你不能因为一个额外功能，而加入新的文法，而是应该是布局初期就引用可扩展的可能性。（文法的预留是第一位的，而功能的直接扩展能做到就更好）

2 [领域专用语言(DSL)迷思](http://www.infoq.com/cn/articles/dsl-discussion): DSL实践的译者

  __几个常见误区__
  1. DSL目标受众是非程序员(programminglanguage for non-programmers)，而是领域专家，
  比如正则表达式这种可读性是渣的DSL显然是为程序员 员贴身打造(@P: po nes 和mcss的RegExp)
  而成功的例子也很多就是excel
  2. DSL都是整洁的代码， 还是我们的RegExp(@P:继续PO很臭的正则)，
  其实我并不是裸写的。DSL的关键还是解决具体领域的具体问题，为了消除复杂度(复杂度被隐藏DSL本身的实现里，其实可以看做是另一种接口,而这种接口是无法通过逻辑封装来实现的，
    一个贴近业务模型，而一个贴近的是程序模型)。RegExp显然是个优秀的方案.
  。据说代表你DSL入门，就是写个自己的正则实现
  3. DSL并不仅限于文本形式, 也有图形化的DSL. 由于文本编辑的高效性，
  其实也有些图形化的DSL工具，比如邓继峰它们组的人工智能的编辑器，一般是游戏策划使用。
  在这种情况下，文本描述的形式可能不那么容易编辑了，相对策划来讲
  4. SYntax应该贴近自然语言(应该是领域模型)，为什么业务自然语言（Business Nature Language）是DSL的一个重要分支.因为自然语言足够贴近领域模型，比如业务员

3 [DSL流畅接口，几层抽象讲的不错](http://www.cnblogs.com/weidagang2046/archive/2011/10/30/2229293.html)
  语言规则限制了接口的表现能力，不过除了外部DSL这种根本解决方案而言，我们可以在目前的语言规则之下，定义内部DSL（内部DSL并不是接口的封装抽象）
  __基本语义抽象__
  2. 5.times( console.log ). 基本语义抽象
  3. home.to(shool)   <-> new Distance({from:home, to:scool}) 流畅接口一般会有一个内部的context保持状态，以保证返回的都是需要的对象. 传统OO接口中，不会这么设计
一般会

  __管道抽象__
  1. `cat dsl.md | grep '##' | sort`
  2. jQuery的链式更类似于一种管道抽象。 `$('div').css().html().animate()``
    并不是所有的接口都适合与设计成管道风格，因为dom操作的频繁性和顺序性，作成链式可以明显
    的提升可读性。

  __层级结构抽象__
  1. 链式风格的代表的是一种线性抽象。 对于层次结构要求的比如(xml结构，树形接口)，这时层次结构的抽象
    也可以用流畅接口来实现，一般来讲，嵌套函数的方式是最容易构建层级的，因为一层函数就是调用就是
    一层结构。不过这里它用了起始和关闭符号来构建结构
    ```
    var obj =
    $.div({id:’product_123’, class:’product’})
        .img({src:’preview_123.jpg’})
        .ul()
            .li().text(‘Name: iPad2 32G’)._li()
            .li().text(‘Price: 3600’)._li()
        ._ul()
    ._div();
    d.hello()
    ```

    __异步抽象__

    如果一层结构属于一个基本语义对象, 那么如果我们将一次异步操作抽象为一个基于语义对象，
    那我们就可以用抽象前几个抽象的方式来定义我们的异步抽象.

    ```javascript
    $.promise().done(promise).done(promise)
      .and(promise).done().fail();
    ```




4 [使用Dsl改善软件设计 ](http://www.slideshare.net/mingjin/dsl-13468156)
p48 语义模型与构造器, 流畅接口与对应语义模型，内部DSL的不同抽象, 语法可以根据文献3进行查看.

p50 函数序列与链式接口以及嵌套函数.
  1. 函数序列会有全局状态，即context，不稳定. 方
  2. 链式接口每次都返回新的链式构造链, 有全局状态，可读性最高
  3. 嵌套函数是唯一一个可以避免上下文状态的方式，同样也需要全局函数，并且可读性由于括号的存在并不是太好

  最佳实践

  1. 从问题本身了解问题，而不是从解决的起点. 所以设计DSl使用BDD的方式是比较合适的
    这个可能大部分程序员都会犯这个错误，碰到需求，首先就快速脑补实现方案，结果与实际使用的需求不符，到最后可能重构成本就.
    2.
5. [模型驱动开发](http://www.jot.fm/issues/issue_2009_09/column6.pdf)
  1. Page2: 当你设计，涉及到编译原理只是， 流畅接口封装，其实都是有较成书的技术方案了(因人而异)，你也很难做到技术性的突破，就这而言，设计就显得格外重要

6. [如何设计一门语言（十）——正则表达式与领域特定语言（DSL）](http://www.cppblog.com/vczh/archive/2013/09/16/203249.html)
  DSL是不是都很易读？

  1. 最常用功能要提供最简的使用体验
  2. 可读性 &&(||) 紧凑性
  3. 领域模型设计清晰
  4. 设计一个DSL首先要求你对领域本身有着足够的理解，在长期的开发中已经在这个领域里面感受到了极大的痛苦，这样你才能真的设计出一个专门根除痛点的DSL来。RegExp, SQL, HTML(以及GUI构造配置文件)

7. [演化架构和紧急设计: 使用 DSL](http://www.ibm.com/developerworks/cn/java/j-eaed13/)


8. [DSL实践的第一章](http://book.51cto.com/art/201310/412342.htm)

  - 作者第一步就用服务员点咖啡的例子引出了DSL实际上是将问题域->解答域的映射和建模
  - 术语是问题域的关键，而分析模型是第一步。
  - 抽象是人类大脑的认知行为，集中注意到核心层面，而忽略细节。。
  - 用DSL编程时只需要处理问题域的复杂性，你用不着操心解答域的实现细节和其他非必要因素。（关于非本质复杂性的讨论，参见附录A。）因此，多数情况下非专业程序员也能用好DSL，__前提是DSL具备了适当的抽象层次__。数学家能轻松学会使用Mathematica进行工作，UI设计师写起HTML来怡然自得，就连硬件工程师都有VHDL（超高速集成电路硬件描述语言，是一种在电子设计自动化即EDA领域使用的DSL）可用，符合用户的直觉(贴近问题域的模型)。
  - 高层抽象、有限词汇

  - SQL（内部:https://github.com/tgriesser/knex）
  - make(内部: gulp cake)
  - html(内部：定义一个)
  - mocha+should.js -  qunit
  - css 样式描述语言
  - Bison Flex, Yacc, lex
  它们都是有限表达力，即使是内部DSL也不是提供一系列的API，而是贴近领域问题的

如何让我们的内部DSL的语言噪音最小

（) {}等都是不符合我们的

## Writing custom DSL


###外部DSL例子 7:30

1. SQL
2. RegExp
3. Shell


__构建成本__

1. jison   [jison](http://jison.org). 自底向下的解析策略， bison的js版，内建了词法系统
  例子:
  1. handlebar.js.
  2. coffeescript
  3. velocity https://github.com/shepherdwind/velocity.js
2. PEGJS   [https://github.com/dmajda/pegjs](https://github.com/dmajda/pegjs) 自顶向下
  通常来讲自定向下是一个比较简单的，即符合我们常规的思维逻辑，而GLR则很难容易的通过手工构建
  使用PEGJS https://github.com/dmajda/pegjs/wiki/Projects-Using-PEG.js
  总体来讲，使用PEGJS

> [http://jsperf.com/jison-vs-peg-js]()
> 两者的速度基本是5-10倍的差距，当然关键可能是其中的词法部分，jison采用的是正则表达式，词法一般来讲 Lexer + Parser 会占据超过50%的时间。


###一点瞎想

1. 语言展现只是前端，领域模型是后端。即使脱离前端也有其实际意义
2. DSL的构建成本比GPPL要低得多，所以我有条件在软件开发中使用DSl来解决特定的领域问题
3. DSL是不是都很易读容易理解？ 不其然，RegExp, 所以紧凑也是一个方面， 但是它比我们事无巨细的去描述一个字符的匹配规则要容易的多——紧凑或其它特性(面向程序员：领域专家)
4. 软件开发领域所作的一切都是为了抽象，提炼出普遍，分层架构到变量定义都是抽象。，而外部DSL是另一层级的抽象，它是跳脱出语言限制，它抽象了领域模型，并且(待续)
5. DSL 只是協助軟體開發的其中一項工具，我們還必須有靈活的（agile）開發流程（process）、軟體框架（framework）、以及模式（patterns）的搭配


###内部DSL

1. 宿主语言会影响到，作为内部DSL的可能性， 这个例子可以使用coffeescript
2. 构建简单,不需要成本较高的编译原理知识(事实上目前构建外部DSL的成本已经大大降低，稍后我会介绍)
3. 已经有比较成熟和公认的解决方案
  1。 链式

如果你去搜索DSL，会遇到很多的DSL都是关于Ruby，因为什么？ 因为ruby有很多适合元编程的特性. Ruby的圈子，
1. Coffeekup
2. jQuery
3. Mocha 7:00. 无论testcase和reporter
  xx.should.be.an.string. 以及最后的报表, reporter可以做成任何样式，因为地下的领域模型已经构建
  关于should.be如何实现？  defineProperty，当然有个IE6支持的库， should.js
  在runler的同时是

```javascript
descritbe "hello" ->
  it "should work as expect" ->
    a.should.be.an.string

  it "should work"
    a. should.be
```

4. Gulp
内部DSL经常会出现于动态高级语言, 因为它们容易，内部DSL简而言之就是
流式接口和编码约定的结合

1. 流式接口
2. 编码约定


8:00 简化领域模型的

<router route = "/blog">
  <action route="/name" Hello={{Hello}}></action>
  <action route="/hello" Hello={{Hello}}></action>
</router>


## 我做的DSL尝试


我使用过的DSL

0. css, html
1. handlebar 、angularjs、 jake, cake
2. 测试套件: mocha shouldjs gulp casper

多年之后终于成为了精通各技术点拼写的优秀工程师。

其实和大家

我设计实现过的DSL

1. 链式接口(内部DSL)
2. math(内部DSL)
2. MCSS(外部)
3. nm(外部)
4. Regularjs(外部)

## DSl书籍的记录

1. 关键的是DSL的定义，号称DSL之父的给出了一个比较明确的定义 (不过书中也提到有时候，着眼点的不同，可能会导致DSL和GPPL之间的混淆)
2. DSL的关键字定义

### 内部DSL

缺点:
  语法受限，对语言的要求也较大，同样的流畅接口用javascript与coffeescript表现出来完全就是两个感觉

链式接口最接近与口头形式的来表述一个场景，比如BDD或TDD，以及一些有流程性的，比如jQuery.

流畅接口一般，但是在js里 我们可以扩展到原来既有的类(prototype)。所有也有Mootools来直接在原型对象上扩展，比如mootools的contains实现不符合w3c规范，
这个bug影响了V8.


define()

define pro < Class({

})


```javascript

div(name=hello, hello=hello)
  p(name=hello)
  _p(name)
_div()

p.div({})
  .hello()
  .name()
  ._hello()


// 最适合有层次化结构的
div hello:1, name:2
  p hlloe:2
    div name: 1


div({},
  p({},
    span({}, "hello");
  )

)

```

// 加入我们使用coffeescript来表达
div hello:1, name:2
  p hlloe:2
    div name: 1


##


//链式接口
gulp.task "clean the repo", ->

  gulp.src xx.file
    .clean "head"
    .minify ''
    .rename "hello"
    .dest "hello"


我 需要 一瓶

## Hello Name spance

## 例子

我们
new Element("div")


__内部DSL的好基友，未来的proxy API__: DefineProperty是js构建dsl的好基友，
但是其实有一个更强的工具(我们思考我们之前的设计 有什么问题？)

html是一个，对未来的CustomElement而言，识别不了的节点被称之为Unresolved Element
而显然我们的方式是穷举不完



此次分享大部分是来自，因为我其实在写完mcss之后才看得到， puer绝对不是我最得意的作品。

定义DSL: 针对某一领域，具有受限表达性的一种计算机程序设计语言。 当然和其它计算机相关概念，比如设计模式等，它的边界很模糊
计算机程序设计语言：这个是根本，否则和一些所谓的行业术语就无法进行区分
语言性：它必须具有语言性，即连贯的表达能力（在当前领域）
受限表达性：这个是为了与通用编程语言GPPL做区分，DSL只有当前领域所需的的最小集
针对领域：只针对具体的领域，其实这是首先表达性的必然结果
在马丁的书里，分为三类，而这里我们只会涉及到前面两类：外部DSL和内部DSL。语言工作平台，这类一般是用以构建DSL的IDE

外部DSL: 自定义语法，各个宿主环境解析这些DSL进行解析或直接执行和翻译。外部DSL会涉及到计算机基础领域的编译原理的只是，但是呢，很浅不必担心，如果在座的是计算机或软件专业，把这里抓起来应该是快的。

内部DSL：寄生于当前宿主环境的DSL，语法功能上有受限性，，在解决某类领域问题时形成一定的一定的风格（一般来讲就是接口），内部DSL强烈的依赖于宿主语言本身的表达能力（比如javascript与coffeescript）。

构建DSL可以看做成一种更高层次的抽象。

抽象： 定义一个变量、函数，模块，Class，甚至架构都称之为抽象，而构建DSL是一种更高层次的抽象，它跳脱（或部分跳脱）了语言本身的表达能力。相当于是底层框架（语义模型）的表现层。


内部DSL和(命令/查询式)API的区别：
在使用内部DSL时，它给人的感觉是一个有连续关系的整句，而不是一系列命令的集合。

ONE_DAY.until(FIVE_DAY).need(WORK). 由于这种内部DSl为了表达性，通常会加入与程序本身无关的辅助词汇(until, needdeng1)，这点在常规的命令、查询 API是不被认可的。这点在test测试套件尤为明显。



这里内部DSL的首先表达性，在于它对所表达内容的自我限制，否则它就仍然只是常规的API而已

外部DSL和GPPL的边界：
1. 受限表达性：正则表达式，DSL肯定不是图灵完备的。

外部DSL和JSON等数据格式的边界。
2. 边界条件是语言性


为何需要DSL

我们需要实际，DSL只是语义模型的一层壳，比如Regularjs而言，你认为，它是个模板，事实上它比任何现存的模板引擎都要复杂，它的难度是建立内部数据格式与实际产生的DOM之间的关联和取消关联，这是它的基础。 而不是用常规的编译原理知识去解析一个看起来毫无新意的模板语法。

DSL使开发效率更高，
因为它的表达能力，我们思考下正则表达式，如果没有正则表达式，我们只能通过按序列读取字符串，它解决了文本匹配领域的实际问题。更，它与语言无关，这也是外部DSL相较于内部DSL的巨大优势，对于个人经验来讲，内部DSL只是一层语法糖。比如NEJ的chain而言，抛开chain做的，这层包装应该可以在200行之内就完成。


解析duiyu1我们而言是提取其代表的语义模型

语法、文法、语义模型。 文法是一种工具， 是一系列的产生式规则，用于指导语法树生成。

对于coffeescript 的  LogicOr   与 js 的 ||  虽然语法不同，但是它们的语义模型是相同的。  而对于 ftl里 的  {} + {a:b}虽然符号与js的+语法一致，但是显然它们的语义模型是不同。不知道我这样描述大家是否清楚了？ 即DSL只是语义模型的一层壳，大部分情况下对于外部DSL而言你可以认为它就是抽象语法树。

我们先讨论，这本书几乎就是领域特定语言的一些提取，但是这本书不建议大家购买，按我个人的实际经验来看，当你对DSL开始有了解时，这本书无法带给你额外的知识，这里我推荐另外一本书《编程语言实现模式》 虽然说龙书，但这本书确实是捷径。当然这两本书其实都与js无关，都是基于java或ruby的，但事实上  领域的比你具体语言的学习成本要大得多，领域内的知识其实是共通的。

问题来了正则表达式是内部DSL还是外部DSL？。当然是外部DSL，对于js而言，它只认为RegExp是一种词法元素，即//包裹的部分。真正解析的是js内部的RegExp实现引擎。所以事实上是否可以嵌入其它语言并不是定义内部还外部的因素，事实上字符串可以承载任何外部文本型DSL。

我在Regularjs的动画部分，引入这样一段 简单DSL， on: enter; delay: 10; class: animated bounceOut; when: hello;

##内部DSL的实现 （同义词-连贯接口：描述更接近语言风格的API）： 如何将分立的句子连贯的组装成

首先我们思考command-query-interface， 命令与查询分离是一种公认的良好实践，前者不应该由返回值（或不该使用其返回值），后者不修改状态但是会有返回值。而使用方法级联，我们显然会破坏这一规则。对于连贯接口，函数是根据上下文来编写，而一般接口一般都是没有上下文状态的。


表达式生成器：内部DSL的解析层。（如果写过简单的解析器的话，其实嵌套函数很接近于常规的生成语法树的流程。）
此时 我们的语义模型，其实就应该是我们底层的命令-command 接口（@example: 我们暂时代替所有的常规接口，这里以NEJ的接口为例+chain）, 而表达式生成器只是一层API层级的浅封装，为了达到我们的流畅接口。并不是所有内部DSL都需要表达式生成器。

一切都是为了理解和可读性.

- 方法级联（链式接口）：需要状态维持->解析数据，（维护在方法级联表达式生成器对象以链式接口而言就是NodeList对象）
- 函数序列：非OO语言的，它的问题是。几乎是需要避免的，1）全局毒药 2）解析数据
- 嵌套函数：结构型强，流程性弱。可以避免解析数据，几乎与解析语法书是一致的。

一般我们都会将这三种放在一起讲，因为它们都是基于函数的

- 命名参数： 对于字面量map的语言来讲在传参的表达性上要更强,主要是提供命名参数, 当然有直接 named param支持的话会更好, 比如Ruby
  ```ruby
  def foo(str: "foo", num: 424242)
    [str, num]
  end

  foo(str: 'buz', num: 9) #=> ['buz', 9]
  ```

  可笑的是mcss其实也支持named Param, 其实很多模板都支持，因为在一个
  产品中基本会有很多的默认配置，往往只需要修改一个参数，这时候命名参数就会很必要有

  ```css
  $box = ($color = $primary, $size=20px){
    width: $size;
    height: $size;
    background-color: $color;
  }
  .box{
      $box($size=20px);
  }

  ```

  支持命名参数的语言？ —— 字面量对象
  ```javascript
  div({title: "hello"})
  ```
  缺点当然会引入额外的语法噪音

  - 闭包 : 交互
    ```javascript
    computer
      .processor(function(p){
        p.cores 2
      })
      .disk(function(d){
        d.size(150)
      })
      .disk(function(){

      })

    computer
      .processor (p)->
        p.cores 2
        p.i386
        p.speed 2.2
      .disk (d)->
        d.size 200
      .disk (d) ->
        d.size 100

    ```
    这样的好处是,你可以明确的知道当前在操作什么对象，并且可以精确的控制你的执行顺序
    因为你传入的是一个函数，何时运行取决与你,
    一个processor函数只需关系自己的逻辑

    缺点么很明显,也是语法噪音

- 解析树
  往往内部DSL不会生成解析树，但是事实上在流程接口，当然这回引入额外的开销
  如果使用嵌套函数，你会发现内部DSL与外部DSL的区别就仅仅发生在解析阶段了
  即用函数调用取代了常规编译原理的Parser流程。

- 字面量存取器扩展，最大化的减弱语法噪音

  `5.days.ago` 五天之前

  我们在js里可以实现吗？
  function TimeRange(ts){
      this.ts = ts;
  }
  Object.defineProperty(DayRange.prototype, 'ago', {
    get: function(){
      var now = +new Date();
      return new Date(now - this.range)
    }
  })

  Object.defineProperty(Number.prototype, 'days', {
    get: function(){
        return new TimeRange(this * 24 * 3600 * 1000);
    }
  })

  现实中的字面存取器的例子[tj/should.js](https://github.com/tj/should.js)
  ```javascript
  var should = require('should');

  var user = {
      name: 'tj'
    , pets: ['tobi', 'loki', 'jane', 'bandit']
  };

  user.should.have.property('name', 'tj');
  user.should.have.property('pets').with.lengthOf(4);
  ```

- __动态接收__

  动态语言的问题救星，一般问题都会出现在运行时，比如5.days的days没有定义时
  ,如果有了动态接收特性，这个问题将可以迎刃而解.

  以大Ruby为例,只要实现method_missing方法，所有未定义属性就是由这个方法动态接收
  这样的好处是？

  1. 一个简单的例子

  ```javascript
  p.div()
    .hello()
    .slideshow()
  ```
  毫无疑问
  ```
  TypeError: undefined is not a functionmessage:
  ```

  但是假如有动态接收问题就可以迎刃而解，但是JS支持动态接收吗？？？？

  非常幸运？在未来我们可以拥有一种更好的方案来实现动态代理，
  [Proxy](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy)

  ```js
  var proxy = Proxy({ name: "hello" }, {
    get: function(target, property) {
        if (property in target) {
            return target[property];
        } else {
            return "没有值"
        }
    }
  });

  console.log(proxy.age);        // 没有值
  console.log(proxy.name);        // "Nicholas"
  ```

  好吧，我们可以利用这个强大的特性来帮助我们

  ```js
  function Builder(){
    var self = this;
    this.proxy = Proxy(this, {
      get: function(){
        if (property in target) {
          return target[property]
        } else {
          return self.element.bind(slef, property)
        }
      }
    })
    return this.proxy;
  }

  Builder.prototype.element = function(tagName){
    return this.proxy;
  }

  var b = Builder
  ```




  动态接收是内部DSL相对与外部DSL的最大的不足，因为对于外部DSL而言，一个词法元素
  例如IDENT 可以是一组规则例如 /\w+/, 所有类似 Hello, Home都满足这组规则
  我们无需事无巨细的描述它，而内部DSL归根到底只是一组语法糖，对于没有定义过的
  属性和方法就束手无测了， 而Proxy等类似动态接收的出现，将弥补这一巨大的不足。





## 外部DSL的实现
大部分我们涉及到的问题是，是不是需要AST这一层。

__语法分析策略__

文本-> 语法结构的结构化过程。

1. 分隔符指导翻译: 适合语法简单的场景

![](http://knockoutjs.com/img/homepage-example.png)

knockout 显然是一个典型的分隔符指导的例子。它的所有的binder都集中在data-bind
而不是angularjs一样 分离在各个ng中. 分隔符指导的翻译只需要简单的split+正则就可以
帮助我们解决问题。

然后一旦语法变得复杂，分割指导的方案将不再凑效

2. 语法指导翻译，BNF, 这里我不再想讨论BNF是什么。简而言之他是描述一个语言
对于不是很了解的同学，或者和我一样不是计算机相关专业的同学，我这里
推荐一本[编程语言实现模式](http://book.douban.com/subject/10482195/). 这本书从头到尾的核心理念就是够用就好，龙书虎书，固然权威，但是实际使用
才是根本。
![dada](http://img5.douban.com/lpic/s7661036.jpg)

语法指导翻译即根据文法规则(BNF)按从文本解析为AST

词法解析 -> 语法解析 -> （AST树改写） -> 执行
                                 -> 翻译

1. nes 来讲，只有词法和语法是杂糅的
2. mcss来讲


1. 逐字符
2. 利用正则表达式


这里我只以mcss为例，简单介绍下，整个流程非常规整，只在词法部分与常规的
的做法有所区别



## 外部DSL的javascript

1 词法：  == 正则Trunk > 分离的正则。 但在表现力上，常规基于字符的词法解析能力要更细。但是显然基于正则的实现要更简短些
2
## 内部DSL  VS 外部DSL

1. 学习曲线
  毫无疑问，内部DSL更低，它说到底仅仅只是在现有编程语言之下的接口包装器。
  而外部DSL，你必然会去处理那些可能你不再熟悉或完全不知情的
  文法、词法解析、语法解析、抽象语法树等概念，如果再复杂点可以还要去接触jison、bison之类的解析生成器
  。相对来讲我是在对正统编译原理一知半解的时 通过实践逐步结合进了javascript的灵活性.
2. 创建成本
  一旦熟悉了，两者的构建成本是类似的。即通过表达式生成器和常规的语法指导翻译
  它们的成本是类似的.
3. 执行成本
  而外部DSL有额外的文本解析开销(这个开销非常大), 而内部DSL，
  只是函数调用执行成本更低。但是一般封装良好的解析部分，如果AST可以序列化是可以做到
  预解析的，比如regularjs，真正打包上线的只是序列化的AST数据，完全没有
  解析成本，从这点而言，两者打平。

4. 语法灵活度 & 功能灵活度
    表达能力上明显是外部DSL更强，这点毋庸置疑。但是在功能灵活度来讲，
    内部DSL更强，因为它可以使用原宿主语言的API和功能(即可以与GPPL结合)
    这里一个最明显的例子就是构建工具 gulp,

5. 程序员熟悉度
  内部DSL可以继承宿主语言的编辑器功能，而外部DSL你需要乐此不疲的去增加
  一个个IDE的支持.
6. 领域专家沟通
  显然是外部DSL，因为它完全的人格独立。
7. 强边界:
  灵活度太高其实并不一定是好事，外部DSL限制了只在其规定语法内生效，
  这可以带来更直直观的表达能力


__关于领域模型与语义模型__

两者都指代你的业务逻辑，领域模型是一种实体，被通过类、方法、属性等演化后可以
语义化为源代码。

语义模型与抽象语法树



[Writing Cutomer DSL](https://www.youtube.com/watch?v=lm4jEcnWeKI)

__主题__
1. DSL的历史
  
  低级语言

  ```

  ```

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


2. DSL是什么？ＤＳＬ用来干什么

3. 定义DSL语法的语法
4. 实现你的ＤＳＬ




1. 引入
  1. DSL的出现
2. ＤＳＬ是什么？它用来做什么

  1. Martin Flower 马丁福勒的定义

  2. 可能你听到了会产生的疑惑

  3. 现实里的ＤＳＬ VHDL

  4. 我做过的ＤＳＬ的尝试
    个人是一个典型的精通各技术点拼写




3. DSL实现实践这里会结合进ＤＳＬ与ｊｓ的结合
实现一个内部ＤＳＬ的基本流程
实现一个外部ＤＳＬ的基本流程
2. 内部ＤＳＬ
３. 外部ＤＳＬ
　创建外部ＤＳＬ的工具
4. 内部ＤＳＬ与外部ＤＳＬ


4. QA
