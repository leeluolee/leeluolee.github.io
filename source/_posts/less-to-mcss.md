title: LESS to MCSS 指南
date: 2013-08-05 21:40:04
tags: ['mcss', 'css']

---

##前言

虽然首页没有开始做，昨天仍决定将[MCSS](https://github.com/NetEaseWD/mcss)从身边的基友们开始向杭研推广了，从开始做这个直到现在推广遇到最多的问题是：


> __不是有LESS了吗？__

这个问题回答了很多遍了，但是觉得回答的都不够好，所以觉得写一篇文章解释一下。其实很多答案也都可以从基于MCSS封装的函数库[mass](https://github.com/leeluolee/mass)中得到解答，本文针对MCSS的例子都可以在这个[Try-Page](http://leeluolee.github.io/mcss/)中进行尝试。


<!-- more -->


-------------------

##LESS特性在MCSS中的对应

首先解答下LESS的特性在MCSS中的对应，这几乎也囊括了在实际生产使用时的80%的功能(实际生产并不包含基础类库封装)

###1. 嵌套

MCSS与LESS等其它预处理器的嵌套规则完全一致，支持`&`占位符，例如:

```
.m-home{
    display: block;
    div, ul{
        + div{
            margin-top: 20px;
        }
        border: 2px solid #ccc;
        > a{
            color: #fff;
            &:hover{
               text-decoration: none; 
            }
            ~ span{
                display: block;
            }
        }
    }
}
```

MCSS同时支持另一个占位符`%`，与`&`类似，但它不包含顶级的选择器

比如有时候，我们需要在`.ms-form`的扩展类`.ms-form-stack`中修改某层节点的样式，这时我们不需要重新重复一次书写，例如

```
.ms-form{
    input[type="text"],
    input[type="password"],
    input[type="email"],
    input[type="url"],
    select{
      display: inline-block;
      .ms-form-stack %{
        display: block;
      }
    }
    // other ruleset
}
```

__outport__

```css
.ms-form input[type="text"],
.ms-form input[type="password"],
.ms-form input[type="email"],
.ms-form input[type="url"],
select{
  display:inline-block;
}
.ms-form-stack  input[type="text"],
.ms-form-stack  input[type="password"],
.ms-form-stack  input[type="email"],
.ms-form-stack  input[type="url"],
select{
  display:block;
}
```


__另外MCSS也可以进行`@media`的条件嵌套__



### 2. 变量

变量是CSS Preprocessor的最基本功能，LESS的变量占用了CSS规范中的[at-keyword](http://dev.w3.org/csswg/css-syntax/#at-rule-diagram) (例如`@name`)并以`:`作为分隔, 例如

```
@name: 10px;
```

而在MCSS中，变量的声明是以为`dollar-name`(如`$name`)作为标志

```
$name = 10px;
```

__WHY?__

1. __避免冲突__：
  LESS由于占用`@`, 达到了在词法上与css一致，成就了它看起来最像CSS的美誉，事实上从语法角度讲，LESS可以说是最不规范的，因为它占用了`@at-keyword`, 在css中@at-keyword是作为[`@at-rule`](http://dev.w3.org/csswg/css-syntax/#at-rule-diagram)开始的标志， 这就有潜在冲突的可能性，并且也不利于未来功能的扩展(mcss中所有的功能扩展都是通过自定义@atrule)

2. __赋值符扩展__:
  除了`=`，MCSS中有另外两种赋值符号：
  1. `?=`: 赋值操作只在变量不存在时进行，例如:
    ```
    $effect-outport = true;
    $effect-outport ?= false;
    ```
    此时第二个赋值无效
  2. `^=`: 它可以将赋值定义在全局作用域，MCSS与LESS一样有作用域，所以有时候需要跳脱作用域限制时，这个赋值符就起作用了
    ```
    $global = 10px;
    p{
      $global ^= 20px;
    }
    ```

以上两个赋值符其实都在函数封装时会常用到。



###3. mixin函数

LESS中的`mixin`跟`ruleset`是一致的，不过可以带上操作,例如
```
.size(@width, @height){
  width: @width
}
//使用时
p{
  .size(20px, 40px);
}
```

而在MCSS中，函数可以达到同样效果。首先了解下MCSS中函数的书写方式，与LESS的mixin一样，一个函数可以有参数，也可以没有，同时在MCSS中，函数是一种值类型，同样可以通过赋值操作进行定义，例如:

```
// 带参数
$size = ($width, $height){
    $height ?= $width; // ?= 操作符的作用场景一
    height: $height; 
    width: $width; 
}
// 不带参数
$clearfix = {
    *zoom: 1;
    &:before, &:after {
        display: table;
        content: "";
        line-height: 0; 
    }
    &:after {
        clear: both; 
    }
}

```

使用时候，你可以用类似的括号来调用，也可以用所谓的`隐式调用`, 比如:

```
body{
    $clearfix(); //正常调用
    $size: 5px;  //设置宽高的隐式调用
}
```

__输出__

```
body{
  *zoom:1;
  height:5px;
  width:5px;
}
body:before,body:after{
  display:table;
  content:"";
  line-height:0;
}
body:after{
  clear:both;
}
```


__需要注意的是，MCSS中的函数是一种真正的值类型，它可以被传递进函数，也可以被函数返回(或用`^=`操作符定义在全局)，并保留作用域——所谓闭包__，这不仅仅是个语法糖，使得MCSS拥有其它预处理器没有封装能力！。比较近的例子可以查看MCSS的官方函数库[mass的effect.mcss](https://github.com/leeluolee/mass#effect)，利用它，你可以封装出类似`$swing`的函数，并且可以传入参数进行效果调整。

```
@import 'https://rawgithub.com/leeluolee/mass/master/mass/effect.mcss';

$swing(24deg);
```

__Outport__

```css
body{
  -webkit-backface-visibility:hidden;
}
.animated{
  -webkit-animation-duration:1s;
  -moz-animation-duration:1s;
  animation-duration:1s;
  -webkit-animation-fill-mode:both;
  -moz-animation-fill-mode:both;
  animation-fill-mode:both;
}
@-webkit-keyframes swing{
  20%,40%,60%,80%,100%{
    -webkit-transform-origin:top center;
  }
  20%{
    -webkit-transform:rotate(24deg);
  }
  40%{
    -webkit-transform:rotate(-16deg);
  }
  60%{
    -webkit-transform:rotate(8deg);
  }
  80%{
    -webkit-transform:rotate(-8deg);
  }
  100%{
    -webkit-transform:rotate(0deg);
  }
}
@-moz-keyframes swing{
  20%{
    -moz-transform:rotate(24deg);
  }
  40%{
    -moz-transform:rotate(-16deg);
  }
  60%{
    -moz-transform:rotate(8deg);
  }
  80%{
    -moz-transform:rotate(-8deg);
  }
  100%{
    -moz-transform:rotate(0deg);
  }
}
@-o-keyframes swing{
  20%{
    -o-transform:rotate(24deg);
  }
    -o-transform:rotate(-16deg);
  }
  60%{
    -o-transform:rotate(8deg);
  }
  80%{
    -o-transform:rotate(-8deg);
  }
  100%{
    -o-transform:rotate(0deg);
  }
}
@keyframes swing{
  20%{
    transform:rotate(24deg);
  }
  40%{
    transform:rotate(-16deg);
  }
  60%{
    transform:rotate(8deg);
  }
  80%{
    transform:rotate(-8deg);
  }
  100%{
    transform:rotate(0deg);
  }
}
.animated.swing{
  -webkit-animation-name:swing;
  -moz-animation-name:swing;
  animation-name:swing;
  -webkit-transform-origin:top center;
  -moz-transform-origin:top center;
  -ms-transform-origin:top center;
  -o-transform-origin:top center;
  transform-origin:top center;
}
```

这个不仅仅是LESS，是所有其它预处理器没有的能力！


### 4. 颜色函数

mcss支持hsl以及hsla的色值格式，最终会被输出为rgba或者`#ccc`

与LESS不同的是，MCSS不提供类似`lighten`等动词的函数，统一为rgb概念中的red、green、 blue 和 hsl概念中 的hue、saturation、lightness 以及alpha 这7个通道的调节，函数名分别为`r-adjust`,`g-adjust`,`b-adjust`,`h-adjust`,`s-adjust`,`l-adjust`,`a-adjust` 全部支持相对和绝对调节

比如LESS中`lighten`、`darken`其实就是lightness的相对调节

```
@color1: lighten(#ccc, 10%);
@color2: darken(#ccc, 10%);

```

在MCSS其实就是

```
$color1 = l-adjust(#ccc, 10%); //往亮调
$color2 = l-adjust(#ccc, -10%); // 往暗调节
```

所以MCSS的色值函数需要你对hsl颜色格式有一定的了解(前端开发应该这是必备的基础概念吧)


### 5. 操作符

MCSS支持所有LESS的操作符(或者说其实MCSS支持JS中的二元以及以下的所有操作符，并且优先级与JS完全一致)


  

##一些LESS所欠缺的能力

### 1.逻辑控制`@for`、`@if、@elseif、@else`

由于LESS占用了`@at-keyword`，所以很难提供类似的语言功能。LESS提供一个在选择器上的扩展`when`但是能力仍然有限。


### 2.`@extend`

mixin函数可以帮助我们实现代码片的复用，但是有个巨大的问题就是，mixin会让代码变得庞大(可以看看基于less的bootstrap的重复样式)，当有明显的派生关系时，我们可以使用`@extend`，`@extend`是一个源于SASS的概念，它会将派生类的选择器添加到基础类之后。

```
.u-ipt {
  padding: 5px 10px;
  box-shadow: inset 1px 1px 3px rgba(0,0,0,0.3);
}

.m-form{
    input[type="text"],
    input[type="password"]{
      @extend .u-ipt;
    }
}

```

__Outport__

```
.u-ipt,
.m-form input[type="text"],
.m-form input[type="password"]
  padding:5px 10px;
  box-shadow:inset 1px 1px 3px rgba(0,0,0,0.3);
}
```

没有参数的`mixin`其实都可以用`@extend`来实现(但`@extend`一般用在有明显派生关系的ruleset)，MCSS支持多重`@extend`以及嵌套`@extend`，具体请查看MCSS主页


### 3.`@abstract`

由于组件封装时，我们无法知道后续是否需要某个ruleset，`@abstract`这个@atrule的作用是，将一个或多个ruleset标记为不输出，但是仍然可以被派生。

```
//标记一个ruleset
@abstract btn{
  left: 10px
}
//标记一整个块
@abstract {
  .btn{
  }
  .fbtn{
  }
}
```

你也可以抽象一整个文件, 它是`@import` 的抽象版

```
@abstract 'ui.mcss';
```


比如在团队开发时，`ui.mcss`已经被公有样式`base.mcss` import了(即会被所有页面所共用)，但是后续的页面中仍然需要使用ui.mcss的变量、函数或者ruleset，此时`@abstract出现了`;

__ui.mcss__

```
// btn的mixin函数
$btn = {
  padding: 10px
}

// ui中的ruleset 
.u-btn{
  
}


```

__base.mcss__ 

使用`@import` 会引入`ui.mcss`中的样式

```
@import ui.mcss

```


__page1.mcss__ 

使用`@absctract`，你不会引入任何样式， __但是你仍然可以使用文件中的变量、函数和派生`ui.mcss`中的ruleset__


```
@abstract 'ui.mcss';

.u-btn-2{
  @extend .u-btn; // 仍然可以@extend
  $btn();         // 仍然可以使用变量、函数
}
```

这样可以解决团队开发中的问题。一套代码完全取决于`@import`、`@abstract`和`@media`三者的调用会有不同的表现。





### 4.更好的出错信息以及sourcemap

在出现语法错误时，MCSS会给你更精确的信息

![error图](https://github-camo.global.ssl.fastly.net/9b3c4a1accf639b9dffbc877275e3e6cca9360c7/687474703a2f2f6c65656c756f6c65652e6769746875622e696f2f6d6373732f696d672f6572726f722e706e67)

同时sourcemap v3格式开始被chrome的developer tool的支持，MCSS也支持(需开启MCSS sourcemap选项，并在chrome的开发者工具的实验特性)

![sourcemap](https://github-camo.global.ssl.fastly.net/8933d6c727f1461fbab5592eb48e0e3d778d324c/687474703a2f2f6c65656c756f6c65652e6769746875622e696f2f6d6373732f696d672f736d2e706e67)




### 5. MCSS命令行工具

相对于其他预处理器MCSS的命令行工具参数很简单，并且提供了代码的多种输出格式，以及自动编译的功能，基本上你已经无需其它工具的支持。具体请`npm install -g mcss` 并且`mcss -h` 一下




-----------------------------



##结尾感言

LESS的成功来源于它的`简单`，成功的阐述了`82法则`，同时也起到了普及CSS预处理器的作用，事实上接触并且熟悉了LESS的那些概念之后，接受MCSS或者SCSS都是比较轻松的事情。

如果觉得LESS无法满足你的需求时

> __npm install -g mcss__ 尝试一下吧！



_同时MCSS是个易用的CSS Parser哦_

<br/>
