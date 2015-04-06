title: Why another 
date: 2013-8-4 16:39:51
tags: ['mcss', 'css']

---

I create mcss two years ago. Yes, 在那个时代， Less is very popular and Sass has , and stylus also have. 


But why I also create another css preprocessor? 


Sass or Less need cha to help them. 

Which the css language really lack is --- Abstract , A basic feature in other programing language. So, we create . The big issue is not the auto-prefix stuff, it is the part of the abstract in css, but isn't all.


mcss aims creating a compile-to-css language to help csser writing in.

mcss also integrated some common 最佳实践 in other language, but not implement in all css preprocessor. 

The design of mcss is really simple, only a little things are introduced, but change the way to writing the css.

## da



## mcss长啥样

We will


### 


## 独一无二的特性

### first-class function

### @import and @abstract

@import url("https://github.markdow.ess")  screen;

@media screen 
    @import url()

@abstract scc {}

@abstract 

@abstract url();



### builtin walker

mcss().translate(done).
mcss().inteplate().translate().done(function(){
    
})


## Common最佳实践

All in one things , only the practi. mcss 被贴近于一门真实的语言去设计， 遵循语言本身的能力去解决实际问题， 这可以避免我们去为了某个功能而去引入某些奇怪的语法

### trasparent-call

transparent call is a very clever feature which is first introduced in stylus(rework 的哲学与stylus很像， 在原css的词法基础上取构建css预处理器). It is equals the function all.

__`stylus`__

```


```

__`mcss`__

```
$border-radius: 10px;
```

which

### @extend

###

### best error information

### sourcemap support

## Conclusion

Less is 初始正确， 但后来在一条错误的路上越走越远， Sass可以

For past two years, mcss is nearly a internal project in my teams, serval product use it , But have no voice in . partner use them in sass/less way, beacuse most of their feature. mcss also have.

I haven't attempt to  


