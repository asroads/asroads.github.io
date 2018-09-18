---
title: Cocos Creater 学习笔记--新手上路
date: 2018-06-31 16:55:41
categories: "game"
tags:
- cocos2d
---

### 课前想说的话

随着公司项目越来蔽塞，自己的眼界变得狭窄，得自己开一扇窗，挖一口井，以备不时之需，人生路漫漫，总是要多走多看多思多想。子曰：“学而不思则罔，思而不学则殆”

今天开始学习新的一门学习，其实说新，也不算新了，以前我在2015年的时候我就学习了一段时间的cocos2d-js 3.0，自己还做了一个本地消除小游戏——[candy](https://github.com/jsroads/candy)，地址是:<https://github.com/jsroads/candy>

<!-- more -->

时不我待，时间真快，转眼两年过去了，需要学习一些新的知识，在网上看了一遍，感觉最近学习 Cocos Creator还是一个不错的切入方式。

话不多说，学习开始！go，go，go！

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

### 课后感想

- 在场景内编辑 元素（Node）或者动画的属性 通过挂载组件开发，需要对新的类型的开发流程有新的认知
- 需要学习 Cocos Creator 编辑器 和 [VS code](https://code.visualstudio.com/) 编辑器 当然了 如果 喜欢 其他的编辑器的 也可以 用其他的编辑器 [Webstorm](https://www.jetbrains.com/webstorm/) 、[Sublime Text](http://www.sublimetext.com/)、[Atom](https://atom.io/)。