title: 开始使用Hexo
date: 2013-03-02 11:34:51
tags: ["tool"]
---

去年毕业后配置Otcopress完后就没用过了,反正不会去折腾Ruby，发现问题也不好解决，索性就换到[Hexo](http://zespia.tw/hexo/)了, 文档很丰富, 没有累赘的功能部署很快, 简单记录下

<!-- more -->

## 安装

直接npm安装:

```
npm install hexo -g
cd/ path/to/your/blog/root
hexo init {your_site_name}
```

回忆下Otcopress在windows的安装简直不堪忍受, 数小时之久(当然我不熟悉Ruby这个生态圈也有关系)。

## 配置

主要是github的配置, 这里记得如果username.github.com这个仓库已经有了资源，要进行手动清理. 还有一个就是yaml里的deploy项要填写完整的Repository 地址(坑1), 如:

``` yaml
# Deployment
## Docs: http://zespia.tw/hexo/docs/deploy.html
deploy:
  type: github
  repository: https://github.com/leeluolee/leeluolee.github.com
  branch: master
```

如果是项目的gh-pages, 则branch填gh-pages。完了之后如果还不行就进入`.deploy`目录手动添加一个名为__github__ 的remote branch，例如
`git remote add github {你仓库名}`




## 书写

hexo支持摘要断行 `<!-- more -->`


## 坑

1. 尽量将source和page分在不同的repo里
2. 关于swig模板与代码高亮的冲突: {%raw%} {%endraw%} 即可

## 结尾

Hexo是一个很精简的blog generator && deploy, 完全满足技术人员日常的博客需求, 使用过Octopress的应该是完全没有学习成本, 本人是典型极简癖, 跟这个Hexo简直是一拍即合






