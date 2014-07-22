title: GitBook: Git + Markdown 快速发布你的书籍
date: 2014-07-22 15:39:17
tags: ["gitbook", "tools"]
---


[gitbook](https://www.gitbook.io/)是一个用于发布个人书籍的平台，类似于国外著名的LeanPub. 其中一个很大的特点是它利用git作为版本管理和发布工具, 加上是在线形式，你可以很方便的进行作为的快速更新. 


<!-- more -->

gitbook提供了一个简单的命令行工具`gitbook`用来编译和预览的书籍.

[![NPM](https://nodei.co/npm/gitbook.png?downloads=true&stars=true)](https://nodei.co/npm/gitbook/)



## 安装

你可以直接通过npm安装gitbook到全局

```shell
npm install -g gitbook
```

gitbook只提供了如下四个命令

```shell
$ gitbook -h
Usage: gitbook [options] [command]

Commands:

build [options] [source_dir] 编译指定目录，输出Web格式(_book文件夹中)
serve [options] [source_dir] 监听文件变化并编译指定目录，同时会创建一个服务器用于预览Web
pdf [options] [source_dir] 编译指定目录，输出PDF
epub [options] [source_dir] 编译指定目录，输出epub
mobi [options] [source_dir] 编译指定目录，输出mobi
init [source_dir]   通过SUMMARY.md生成作品目录

Options:

-h, --help     output usage information
-V, --version  output the version number
```


## 书写

就以我为 [regularjs](http://regularjs.github.io) 写的 [指南](http://regularjs.github.io/guide/)为例，一份gitbook的源文件目录一般是这样的.

```shell
$ tree -L 2 -U

├── _book
│   ├── index.html
│   ├── en
│   ├── gitbook
│   └── zh
├── node_modules
│   ├── gulp
│   └── gulp-gh-pages
├── en
│   ├── syntax
│   ├── core
│   ├── getting-start
│   ├── advanced
│   ├── SUMMARY.md
│   ├── README.md
│   ├── introduct
│   └── qa
├── zh
│   ├── syntax
│   ├── core
│   ├── getting-start
│   ├── advanced
│   ├── SUMMARY.md
│   ├── README.md
│   └── introduct
├── LANGS.md
├── cover_small.png
├── gulpfile.js
├── package.json
├── book.json
└── README.md

```

__几个关键文件的说明__

* __LANGS.md__
  当你需要发布多个语言版本时，根目录只需要放置一个LANGS.md, 格式如下

  ```markdown
  * [English](en)
  * [中文](zh)
  * ...
  ```
  每个zh，en文件夹现在就相当于一个独立的书籍.
  
* README.md
  REAME相当于书籍的前言部分, 可以忽略
* cover_small.png 和 cover.png
  书籍的封面图

* __SUMMARY.md__
  SUMMARY是最重要的一个部分, 它创建的是整书的索引, 你也可以通过`gitbook init`读取`SUMMARY.md`来生成目录结构. 格式如下
  ```markdown
    * [前言](introduct/README.md)
      - [API索引](introduct/index.md)
    * [你好 Regular](getting-start/README.md)
      - [安装](getting-start/install.md)
      - [快速起步](getting-start/quirk-example.md)
      - [小结](getting-start/review.md)
    * [内建模板引擎](syntax/README.md)
      - [语法说明](syntax/syntax.md)
      - [插值](syntax/inteplation.md)
      - [逻辑控制](syntax/if.md)
      - [循环控制](syntax/list.md)
      - [动态引入](syntax/include.md)
      - [表达式](syntax/expression.md)
      - [小节](syntax/review.md)
    * [核心概念](core/README.md)
      - [类式继承和组件定义](core/class.md)
      - [数据监听](core/binding.md)
      - [directive——指令](core/directive.md)
      - [filter——过滤器](core/filter.md)
      - [event——ui事件体系](core/event.md)
      - [regular的模块化策略](core/use.md)
      - [简单事件发射器emitter](core/message.md)
      - [小节](core/review.md)
    * [高级特性](advanced/README.md)
      - [内嵌组件](advanced/component.md)
      - [regular的transclude](advanced/content.md)
      - [小节](advanced/review.md)

  ```

接下来就是依次完成你每个章节的书写了, 你需要开启`gitbook serve .`来进行实时的web预览(服务器默认为`localhost:400`)

markdown的标准格式可以看[这里](http://daringfireball.net/projects/markdown/syntax)，现在的程序圈的markdown包括gitbook普遍使用的是[GitHub Flavored Markdown](https://help.github.com/articles/github-flavored-markdown)，除了github中已经说明的那些, 它还支持一些额外的小特性, 比如`[x]`可以用来设置一个checkbox来实现todolist的功能.
  

## 发布

gitbook的命令行工具不提供对发布操作的支持，你可以直接使用`git`发布，首先你需要添加gitbook的仓库作为你的一个远程库. 比如regularjs的路径为

```shell
git remote add gitbook https://push.gitbook.io/leeluolee/regular-guide.git

git push gitbook master
```

在push成功后，gitbook.io会自动在服务端进行build. 你可以在gitbook.io的个人主页上查看到build信息.

## 常见问题

1. gitbook 好卡啊 我可以发布到我的个人网站吗?
  当然可以，`gitbook build`之后的`_book` 就是一个完整的web目录, 你可以放置到你的个人网站上.

  一个更好的做法是直接发布到github的gh-pages上, 由于gitbook每次build都会重新生成整个目录.所以你需要利用`gulp-gh-pages`或`grunt-gh-pages`等工具进行发布.  
  
  你可以参考我的[做法](https://github.com/regularjs/guide/tree/master), 这样一键`gulp deploy`可以完成指定目录`_book`发布gh-pages.
  
  
  










