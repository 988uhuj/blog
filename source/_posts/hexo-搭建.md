title: "Hexo 搭建"
date: 2015-03-14 21:43:52
tags: hexo
---
一场奔波过后，这段时间得空可以整理一些平常所学。个人博客也是熬了许久转到今天的Hexo。之前的CSDN，WordPress，Octopress效果总是不尽如人意,当然这也是跟自己文墨不深有关。今日偷闲整了一个Hexo博客，安装过程相对来说比较简洁，没有遇到太多的坑。
<!-- more-->
以后会用的几个点整理了一下：
###安装环境
* win7 64
* node 0.12.0
* npm 2.5.1

我直接到官网下的installer，安装完直接可用。

###安装教程(v3.0.0)
```
npm install hexo-cli -g
hexo init blog
cd blog
npm install
hexo server
```

###部署到GitHub
先安装deployer，需在blog目录下
```
npm install hexo-deployer-git --save
```
再修改_config.xml：
```
deploy:
  type: git    #部署类型
  repository: git@github.com:chenupt/chenupt.github.io.git  #部署的仓库的SSH
  branch: master   #部署分支,一般使用master主分支
```

###主题
主题使用在知乎上的一个推荐：[NexT](https://github.com/iissnan/hexo-theme-next)，效果很赞。

###其他
生成部署简洁命令：
```
hexo g -d
```
查看更多：
```
<!-- more -->
```