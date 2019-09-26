---
title: Cocos Creater 学习笔记--新手上路
categories: game
tags:
  - Cocos Creator
abbrlink: 18d6ec33
date: 2019-09-02 19:55:41
---

### 前面的话

去年短暂的学习了一点，后面就转战Layabox学习了，一年多的马不停蹄的开发，有了很多对程序和项目的认识和理解，人总是要有些追求的嘛！时光转了一年，如今重新拥抱[Cocos Creator](https://www.cocos.com/docs) ，也有了新的感受。技术真的是日新月异，这次重新踏入 Cocos Creator 希望能够学有所成，对自己的技术提高一个档次，三大引擎，Cocos Creator，Layabox，Egret ，此次再次去深入理解和掌握 Creator。

<!-- more -->

2018年-06-31 记录

> 随着公司项目越来蔽塞，自己的眼界变得狭窄，得自己开一扇窗，挖一口井，以备不时之需，人生路漫漫，总是要多走多看多思多想。子曰：“学而不思则罔，思而不学则殆”
>
> 今天开始学习新的一门学习，其实说新，也不算新了，以前我在2015年的时候我就学习了一段时间的cocos2d-js 3.0，自己还做了一个本地消除小游戏——[candy](https://github.com/jsroads/candy)，地址是:<https://github.com/jsroads/candy>
>
> 时不我待，时间真快，转眼两年过去了，需要学习一些新的知识，在网上看了一遍，感觉最近学习 Cocos Creator还是一个不错的切入方式。
>
> 话不多说，学习开始！go，go，go！
>

### 课前预习

打开官网 <http://www.cocos.com/>

下载 <http://www.cocos.com/download>

文档 <http://www.cocos.com/docs/>

### 课间笔记

需要理解 Cocos Creator 是一套完整的 游戏开发的解决方案

- 主要特征就是官网说的 脚本化开发，组件化开发，数据驱动化开发。
- 主要是以内容创作为核心 属于Cocos2d-x 引擎的优化版做基础，然后实现编辑器环境一体化。
- 抛弃了以前Cocos2d 的 传统的继承为主，改为组件挂载这样的组合模式开发
- 继承了大部分以前的Cocos2d 的语法和API 但是底层的机制已经得到了很大的不同，所以并不能把以前的cocos 项目直接 改成 Cocos Creator项目
- 主要用 JavaScript和 Typescript 做为开发脚本 底层用 JS Binding 运行 cocos2d -x 引擎 调用用 C++原生高性能。
- 游戏运行的单位是 场景 Scene 为内容中心创作 可以使用内置组件 和自定义组件 或者 预制（Prefab）
- 游戏主要以 节点（Node）为结构树，用组件挂载的方式实现逻辑，我们可以理解成 Node是一个实体，只具有最基本的一些属性，比如位置、透明度，角度，等一些属性，更多的逻辑处理，需要我们用引擎自带的组件或者自己写的组件进行组合，这样就形成了我们最终的产品。
- 本次学习，采用Typescript学习，一年前那时候用的是JavaScript，如今typescript基本被行业认可和接受，越来越多的教程都是typescript写的。
- 上次学习，采用了 官方推荐的Vscode 编辑器，本次采用 Webstorm为主 和VScode为辅的学习，编辑器只是一种工具，我们用工具的目的是提高效率，而不是执着于工具本身。

### 课后感想

- 在场景内编辑 元素（Node）或者动画的属性 通过挂载组件开发，需要对新的类型的开发流程有新的认知
- 需要学习 Cocos Creator 编辑器 和 [VS code](https://code.visualstudio.com/) 编辑器 当然了 如果 喜欢 其他的编辑器的 也可以 用其他的编辑器 [Webstorm](https://www.jetbrains.com/webstorm/) 、[Sublime Text](http://www.sublimetext.com/)、[Atom](https://atom.io/)。