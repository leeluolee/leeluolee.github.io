
先回答题主的问题, 再来分析下 @司徒正妹 和 @尤雨溪 的回答. 一不小心写长了。

1. 普及mvvm系统的常识

对Vue、Avalon都不是很了解(虽然我有个作品在API上受到了Vue的很大影响), 但是从表现来看， 应该与Angular一致， 是由根节点(组件根节点或文档根节点)的一次自顶向下的递归bootstrap完成的.  

前年， 我写过一个与上面类似的dom-based的mvvm系统, 只有1500行，所以功能上会有欠缺， 但是基本流程我觉得应该是一致的， 所以可以先来解释下, bootstrap一个节点大概流程。

1. 找到节点名, 看是否做对应处理(如angularjs的element的directive， vue的已注册组件)
2. 找到所有属性名, 看注册情况判断处理成指令或普通属性(可能有插值)
3. 遍历子节点并bootstrap这些节点,即返回步骤1, 注意遍历中可能碰到textNode，此时要检查是否有插值存在 如 `<h2>{title}</h2>`

1,2,3顺序的流程可能所有不同, 就是所谓的前序、 中序、后序的 差别. 我之前写的小玩具是1,3,2的顺序

而knockout从其一致的data-bind来看， 其实解析时， 会比上面三者都要简单， 只要找data-bind对应的属性就行了. 甚至如果你不考虑上下层级关系, 直接用querySelectorAll('[data-bind]')来处理，其实也没多大问题.


2. 好了进入正题. 解答下题主的问题

题主的问题是如何解析和运行这段__表达式__， 它恰恰是vm层的重中之重.

先抛开`eval`和`with`， 因为完全不必去理会这两者，首先是eval,  eval与new Function的最大不同是， new Function返回的是函数, 而eval是直接在当前环境下执行， 其次才应该是它们的作用域的区别。 __仅仅从第一点看，我们就不应该使用eval__， 而是使用 new Function 生成一个可复用的函数供后期运行表达式使用。 因为 无论 new Function 还是 eval其实都不是善茬， 是性能杀手。with 我觉得不必再说了，虽然它可以简化你处理表达式的难度。

两位大大都提到了Angularjs， @尤雨溪也PO出了angularjs的相关源码, 非常意外! 我看到了和我之前预计不相同的结果. Angularjs应该是在酝酿一个很大的计划, 请听我慢慢分析

我们先来看下Angularjs1.3的源码(我认为PO主指的应该是目前可以用的1.3版本)

代码在这里， https://github.com/angular/angular.js/blob/v1.3.10/src/ng/parse.js

我们发现是没有 小尤同学 提到的AST这个中间状态存在的, 整个流程是再简单不过的, 逐字符的tokenize 到 LL的parsex: __特点是parse直接在consume的同时，直接生成了可运行的嵌套函数闭包__, 所以 司徒正妹 提到的new Function的在1.3版本也是有问题. 举个简单的例子来说明下Angular是怎么处理的

以 `1 + 2` 为例. 

https://github.com/angular/angular.js/blob/v1.3.10/src/ng/parse.js#L627

所以返回的应该是这样一个函数 https://github.com/angular/angular.js/blob/v1.3.10/src/ng/parse.js#L104

```
'+':function(self, locals, a, b) {
  a=a(self, locals); b=b(self, locals);
  if (isDefined(a)) {
    if (isDefined(b)) {
      return a + b;
    }
    return a;
  }
  return isDefined(b) ? b : undefined;},
```

即完全没有使用到new Function, 而是直接用嵌套闭包的方式来生成我们需要的函数. 所以在这个版本的对应上， 两位大大的回答都是有欠缺的。


但对于1.4+的版本， 就基本没有问题了, Angular1.4+的表达式相关流程是


```
Toknizer -> Parser(输出AST) -> Interpreter
```

Interpreter https://github.com/angular/angular.js/blob/master/src/ng/parse.js#L1226 的工作和我之前说的Angular1.3的所谓Parse并无多大不同，输出的还是函数（没有牵扯到new Function）

区别就是， Interpreter是从已经树形结构化的AST获取信息，而不是从线性的Token流直接consume.  为啥这么做？ 要加上纯粹的Parse这个环节？  答案就在新版的Angularjs真正有了表达式的Compile过程. 在Angular1.4+上还有另外一条截然不同的线: https://github.com/angular/angular.js/blob/master/src/ng/parse.js#L752

```
Toknizer -> Parser(输出AST) -> Interpreter
```





然而为什么要做这个处理？ 


本来觉得尤雨溪的回答有问题



虽然我目前完全不用Angularjs有些时间了， 但是其实我在2012年jsconf【沪js】之后就关注使用它了。记得那时候它的star才1000多。 所以还算有些了解，并且Regularjs的vm层使用脏检查同样是受到了Angularjs的重度影响。 先点下两位大大的小瑕疵。


首先，司徒大大的。

Angularjs是用到了new Function, 但并不是用在Parser的主逻辑里， 我直接贴代码吧


司徒大大的话其实说的不错，Web前端的 MVVM 比 MVC要实现， MVC只要做好分层和抽象就好， 但是MVVM除了分层抽象之外却同时绕不过DOM这个脏东西，并且会涉及到模板的一些东西，所以从技术难度上讲师会高一些。   

这段代码同时也点到了小鱿同学的一个小瑕疵， 就是其实Angularjs并没有生成中间AST，而是直接生成了嵌套函数。

可以说从这点上， 两位大大互为补充，就算完全正确了。

回到我们问题本身。提问者既然只提到了表达式， 我也就只回答表达式相关的问题。

好我们先分解问题或是来总结下以上两位大大的回答， 我们从模板中可以得到什么类型的初识信息？  显然是字符串，并且对于vue、angularjs、knockout、avalon都是从node.getAttribute、textContent获取的信息，所以也只能是字符串。

然后我们需要得到什么？ 一个封装了可以运行这个包含表达式的逻辑，对了，其实就是函数， 所以问题被分解为了。

解析 ||  函数生成。

解析
     - 强解析
     - 弱解析： 

使用弱解析 + new Function:  不必在parse上下太多的功夫, 但是如 Vue、Avalon.但是呢在代码生成上要
使用强解析 + 函数（闭包）的好处: 不必在代码生成上下太多功夫，但是可以得到完全一样的效果，并且可控性更强（因为）
使用强解析 + new Function: 逻辑上最为复杂，但是会带来不一样的。
使用弱解析 + 函数（闭包）: 唯一的好处就是绝对的简单，代码量绝对的小。 市面上大部分 声称花了xx天就实现了个Angularjs的mvvm众们。要知道，一个东西做到90% 和 做到95%完全是天与地（这段其实我是纯吐槽）

为什么说强解析 + new Function 是个， 就是parse + 代码生成  输出的是字符串， 这使得输出结果可以被序列化。 为什么说Angularjs本身并没有什么错，因为Angularjs和Vue、Avalon一样 是从Dom节点上递归遍历来得到解析结果的， 既然绕不开Dom， 它增加一个code gen 也就没有任何意义了。

所以如果仅仅只是表达式， Regularjs使用强解析 + new Function其实并没什么软用。 关键是Regularjs的整个模板都是自己的parser， 这就有用了。  这使得， Regular的模板是可以被预解析的(目前已经有requirejs-regular和regularity插件处理). 

从源码可知， PO地址。

就Parser而言， 我相信可能在mvvm领域， parser在regularjs框架里的权重实现算是比较重的了，但是我仍然认为值得这么做。Parser越重，即代表你可以预处理的逻辑就越多，上线的

再扯远点，其实mvvm的vm层才是兵家必争之地， 一直想写一篇深入的文章来做个全面的对比， 到时候找个。


当一个组件只调用一次的时候，完全没有问题。 当这个组件（模板）被实例化多次的时候， 这个，因为整个信息都保存在了AST这个轻量化的数据结构上了（并且，new Function只会在第一次被访问Expression时被）


基于正则， 但是当你不是走一个完整的文法约定的流程时， 你的表达能力是受限的， 你不知道哪些表达式是支持的，哪些可能就会发生错误。  而完整的流程的结果就是， 我可以直接任意复杂的ES5表达式组成。

何况parse的

好， 我们最后来总结下。

e


最最后， 顺便加个广告吧。  Regularjs， 虽然文档一度停滞，因为一直有更紧急的事务需要处理。事实上在网易内部推广的算是非常顺利，有点出乎我的意料，现在已经决定提高优先级去维护它。 目前公司事情， 好在现在有了一个靠谱有的新人入职，相信很快可以把regularjs的相关配套搭建起来。
