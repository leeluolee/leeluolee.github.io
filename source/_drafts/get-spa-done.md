# 使用Regularjs开发数据驱动的单页面




我们不玩虚的，这次我们直接，对于细节，Regularjs其实已经提供了中英文文档, 博文主要还是带领大家实战为主.

今天我们不来todomvc这种虚的demo了，我们直接来看下如何结合来开发一个单页应用。

当然，对于现在目前的前端而言，甚至前端


从设计初期，regularjs就定位自己为__纯粹的库__，没有杂糅进模块系统，也没有过度的封装dom操作，并且保持自身的无依赖，这使得它可以很容易的集成进任何框架和模块系统中作为view层组件使用。事实上，regularjs已经在一个网易的较大型新晋项目中验证了这种可行性。

既然如此, 单靠Regularjs本身当然是没有独立能力去构建一个复杂的单页面应用的，我们另外一个专注于routing的类库, 这里我们没有选择市面上已有的路由库，而是选择自造了一个，stateman 意为state manager，这里我们不会对其做深入的介绍，毕竟今天的主角并不是它，有兴趣的关注stateman的github，绝对是良心之作

借助同事负责的一个新系统PAT, 我完成了对stateman结合Regularjs的封装，几乎在完全不操作dom的前提下实现了数据驱动的单页面系统的开发，并且最终开发的优雅程度事实上已经超过了我的预期.


好了自卖自夸的话少说，言归正传，看看我们要做的系统长啥样

其实，Regularjs最大的优势就是，是__已经切实的在大公司内部使用的__，不是一个个人的项目，或大或小的产品团队都有，并且这个已经成为了作者的中期里程碑计划之一（所以不用担心这个库会半路挂掉），并且作者本身并没有脱离产品开发，可以避免regularjs偏离实际应用场景。在大产品中可能以组件的方式寄生存在，而在较小的产品和独立模块中也可以很容易的构建出你的SPA系统。

> “能屈能伸”　才是好库

## 指引

1. 贯穿全文的例子


### 示例的技术选型

1. bower : 包管理
2. requirejs: 模块系统
3. requirejs-regular: requirejs的regular模板预解析插件，如果你的项目够小不需要预解析，也可以用requirejs官方的`text`插件来代替　对于使用browserify的同学也有regularify供大家选择
4. stateman: 路由支持
5. regularjs: 组件模块
6. bootstrap.css: 无它, 真的没空去设计这个demo的样式了

1. regularjs: 模块系统
2. bower : 包管理
3. requirejs-regular: requirejs的regular模板预解析插件，如果你的项目够小不需要预解析，也可以用requirejs官方的`text`插件来代替　对于使用browserify的同学也有regularify供大家选择
4. restate: 提供路由支持，stateman的简单封装, 为的是更好的集成regularjs，这个封装很简单百行以内，相信大家都看得懂
5. bootstrap: 无它, 真的没空去设计这个demo的样式了, 用bootstrap搭个能看的吧

###示例的结构

![xx](1)


## 第一步: 模块切分

常规的路由库一般的思路是这样的　ｂａｌａｌａｌａｌａｌａ，这种思路的路由只适合做，如果做复杂应用，你自己做分层处理，因为浏览器与服务端的路由有个最明显的区别，而stateman可以说是ui-router的思想的衍生, 不过也有很多差别
常规的路由库一般的思路是这样的　ｂａｌａｌａｌａｌａｌａ，这种思路的路由只适合做，如果做复杂应用，你自己做分层处理，因为浏览器与服务端的路由有个最明显的区别，而stateman可以说是ui-router的思想的衍生, 不过也有很多差别，毕竟，官方对于stateman做了基于两种类库的封装: 1) regularjs  2) reactjs

reactjs 仅仅发布了一篇博文，有兴趣的同学可以帮我翻译一份


如图所示，以博客详情与博客列表所示，本APP显然可以切分三级结构

1. app: 负责头部导航和侧边栏导航
2. app.blog: 博文
3. app.blog.detail:
3. app.blog.edit:
3. app.blog.list: 博文列表
4. app.chat: 完全杜赞

__注意, 模块划分应该以模块视觉构成为主，而不是击逻辑__
=======
2. app.blog: 内容区域
3. app.blog.detail: 
>>>>>>> 40102278ab31780efdddd28a4e218bc5693e4f2a


好我们先不急着进入下一步，访问 !#/app 看看

我们可以看到，但是显然这个只有一般显示的状态是不该被显示的,　代表是不是直接定位目前

### 创建项目的基类

由于Regularjs中，组件同时也承担命名空间的作用，所以在非小型项目中，我们会统一创建一个基础类，这里我们统一创建一个BaseComponent

同时, 我们给Basecomponent　添加一个过滤器，这样所有继承自BaseComponent的类都将会受到影响, 而直接继承自Regularjs，这是Regularjs得以在工程中保持良好封装性的基础所在。

### app-menu：我们的主导航栏模块

传入$state参数　(@TODO: regularjs 支持$属性名字)以及data-a的问题

### app-login: 我们的登录模块

### app-message: 全局的统一消息通知，这个我们设计成单例





## 本文的例子

本文要实现一个多级结构的博客系统，所有的数据都是伪造的，所有样式都是从bootstrap拷贝过来的，所以不要幻想这个系统有多美好。

最终我们使用的模块管理是基于AMD规范的`requirejs`, 虽然目前正在尝试的项目使用的是browserify，但作者仍然认为基于`AMD`的开发方式是目前来讲更为保守和安全的方式

![da](da)

最终的例子[请戳这里]()

### restate

其中我们的路由功能使用的是stateman, 为了更好的集成进Regularjs，作者对其做了一层短短数十行的简易封装[restate](), 你可以直接通过bower、npm或component安装。对于非AMD或CommonJS的模块解决方案（如NEJ），restate也提供了一个将stateman打包进去的版本[restate-full.js]()。

restate是一个函数, 它返回一个stateman实例
```js
var state = restate();
```

这里的stateman即一个标准的StateMan实例，不过封装了其中的state函数来使它接受regularjs的组件, 其它所有函数和属性可以直接参考stateman的API手册. 我们主要会使用到`stateman.go` 与 `stateman.nav`　来控制我们的路由.

## 路由配置管理


最终我们的路由配置管理是这样

```js
stateman
	.state('app', Application, '')
	.state('app.index', Index)
	.state('app.blog', Blog)
	.state('app.blog.list', BlogList)
	.state('app.blog.detail', BlogDetail)
	.state('app.blog.tags', BlogTags)
	.state('app.chat', Chat)

```

__Arguments__




其中传入state函数的第二个参数如Application等都是标准的Regularjs组件(无需继承自特定的基类). 以Application为例.
=======

```

其中传入state函数的第二个参数如Application等都是标准的Regularjs组件(无需继承自特定的基类). 以Application为例. 
>>>>>>> 40102278ab31780efdddd28a4e218bc5693e4f2a

__application.js__
```javascript
define(function(require){
	var tpl = require('rgl!./application.html'),
		Regular = require('regularjs');

	retrun Regular.extend({
	    template: tpl
    })
})
```
这里我们使用requirejs的[regular插件](http://github.com/regularjs/requirejs-regular)来来加载我们regularjs模板，在上线阶段，通过requirejs-optimizer, 这些模板会被插件预解析为AST.

我们再来看看我们的application.html

__application.html__
```html

```

模板主要，需要注意的是，由于regularjs本身是动态模板，所以当数据变化后，dom结构会进行局部更新，你需要做的仅仅是按以往使用mustache或jst的使用经验描述你的模板结构, 然后在业务逻辑里专心处理你的数据即可。

这里我们可以发现在

```html
<div ref=view> </div>
```

[__ref__](#)是在regularjs `v0.3.0`引入的一个特殊属性. 用来标示模板里的元素节点或内联组件。

上面这段模板描述使得[ref=view]这个节点在组件内部可以通过`this.$refs.view`来获取引用, restate即通过约定的view名称用来放置子组件的内容，依次类推，你可以实现任意层级的嵌套

例如

app.blog.html

```html
<div ref></div>
```

app.blog.detail.html

```html
<div></div>

```

d
```
由于BlogDetail以及是最后一级叶子节点，所以也就无需指定`ref=view`

最终BlogDetail的内容会自动插入到Blog的view节点，而Blog的内容则会插入App的view节点中, 以此类推.


由于Regularjs组件有明显的生命周期, 与Angularjs的隐晦

当你从无状态(页面初始)进入到`app.blog.detail`时，
`blog插入到app` -> `detail插入到blog`

`detail从log中移除` -> `blog从app中移除` -> `app中插入index`

当然这一切都是自动完成的，你所需要做的就是

### this.$state

所有传入state的组件都会在实例化后, 获得一个$state的实例属性, 这个属性即开头的stateman，　由于stateman本身就是一个EventEmitter，你可以将其作为单页系统中的全局中介者来使用，同时用其来控制我们的routing.

### 实现用户登录

### 实现左栏的active状态切换

stateman提供了一个`stateman.is(stateName [,param])`函数来帮我们判断当前state是否满足传入条件

__app.js__中我们有这样一个函数

```js
<li class={ {'z-crt': this.$state.is('app.index') } }> HomePage </li>

```

## 最后

很简答是么， 由于基础，事实上， 我们目前正在做的例子规模

事实上，我们目前正在尝试的系统要远比这个例子复杂，但是仍然保持着良好的可扩展性，而且全程无需去关心dom的开发体验和业务逻辑的清晰度只有真正去尝试了才会知道其中的奇妙。

```javascript

var router = stateful( { view: document.getElementById("view"), Component: BaseComponent })

  .state("app", Application, "");
  if(User.role >= 2 ){
  router
  .state('app.question', Question)
    .state('app.question.list', QuestionList, 'list/:type')
    .state('app.question.manage', QuestionManage, "manage/:type/:method/:id")
  .state('app.testmanagement', TestManagement)
    .state('app.testmanagement.list', TestManagementList,'')
    .state('app.testmanagement.manage', TestManage, "manage/:type/:id")
    .state('app.testmanagement.rank', TestManageableRank,  "rank/:id")
    .state('app.testmanagement.profile', TestManagementProfile, "profile/:tid/:uid" )
  .state('app.group', Group)
    .state('app.group.list', GroupList, '')
    .state('app.group.manage', GroupManage, 'manage/:type/:id')
    .state('app.group.members', GroupMembers, 'members/:id/:type')
    .state('app.group.member', GroupMember, 'member/:id/:type')
  };
  if(User.role >= 3){
    router
    .state('app.knowledge', Knowledge)
      .state('app.knowledge.list', KnowledgeList, '')
      .state('app.knowledge.manage', KnowledgeManage,  "manage/:type/:id")
    .state('app.checker', Checker)
      .state('app.checker.list', CheckerList, '')
      .state('app.checker.manage', CheckerManage,  "manage/:type/:id")
    .state('app.unit', Unit)
      .state('app.unit.list', UnitList, '')
      .state('app.unit.manage', UnitManage,  "manage/:type/:id")
  }
  router
  .state('app.testlist', TestList)
  // test level1
  .state('app.test', Test )
    .state('app.test.list', TestList, '')
    .state('app.test.rank', TestRank, ':id/rank')
    .state('app.test.view', TestView, ':id/view')
  .state('app.exam', TestExam, '/exam/:id')
    // choice or judge.
    .state('app.exam.judge', ExamSimple)
    .state('app.exam.choice', ExamSimple)
    .state('app.exam.blank', ExamSimple)
    .state('app.exam.list', ExamList, ":type(coding|complement)")
    // coding complement
    .state('app.exam.complex', ExamComplex, ':type(coding|complement)/:qid'  )
    .state('app.exam.complex.detail', ExamComplexDetail, '')  //question id
    .state('app.exam.complex.submit', ExamComplexSubmit)
    // need preview or &preview or sid  result?sid&preview
    .state('app.exam.complex.result', ExamComplexResult)
  .state('$notfound', {
    enter: function(option){
      router.nav("/test");
    }
  })
  .state('app.forbidden', Forbidden)
  .on("end", function(){
    console.log(this.current.name);
  })

```

我们创建了一个可以查看首页的，这个例子事实上只有１级路由，因为app本身基本是不会活动了.　它主要是用来维持全局状态，比如边栏、登录信息等等.
事实上，我们目前正在尝试的系统要远比这个例子复杂，但是仍然保持着良好的可扩展性，而且全程无需去关心dom的开发体验和业务逻辑的清晰度只有真正去尝试了才会知道其中的奇妙。

我们创建了一个可以查看首页的，这个例子事实上只有１级路由，因为app本身基本是不会活动了.　它主要是用来维持全局状态，比如边栏、登录信息等等.


