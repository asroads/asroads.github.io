---
title: Cocos Creator如何使用Protobufjs|2.x|3.0
comments: true
categories: game
tags:
  - Cocos
  - Protobuf
abbrlink: d0d75400
date: 2021-04-01 19:53:26
---

这是一篇整合文章，笔者的东西不多，大部分从别处整合得来的，关于[Protobufjs](https://www.npmjs.com/package/protobufjs)的介绍和[Cocos Creator](https://www.cocos.com/)的结合使用，网上和论坛已经有不少人讲解，但随着时间推移，[Cocos Creator3.0](https://www.cocos.com/)发布后很多资料和文章的功能开始不能正常生效了，官方也给出了一种安装方式，本文采对常见的几种给一个总结。
<!--more-->
首先我们知道目前想使用[Cocos Creator](https://www.cocos.com/docs) 和[Protobufjs](https://www.npmjs.com/package/protobufjs) 结合可以直接去商店下载插件，直接使用，但是作为开发者来说，不能知其然而不知其所以然，要让自己工作能够摆脱插件和Cocos Creator不同步造成的问题。

首先我们说几个常见的解决办法

## 常见解决思路

### 商店下载插件

#### 插件一 pb杀手&**pbkiller for Creator 3.x**

- 作者：zxh1982@qq.com

- Creator版本要求 v2.x  和 v3.0.0

**pb杀手**

![resource-icon](Cocos-Creator如何使用Protobufjs-2-x-3-0/store_cocos_com_pbkiller_127167_crop_1503928090.png)

商店地址：https://store.cocos.com/app/detail/1969

**pbkiller for Creator 3.x**

![resource-icon](Cocos-Creator如何使用Protobufjs-2-x-3-0/8701d0f170e60f1295c557b77b05ecd273742.png)



商店地址： https://store.cocos.com/app/detail/2709

这个插件笔者没有使用过，但是看过插件作者的文章，写的很详细，作者也是个很热心的人，更新很及时。

#### 插件二 **protobuf-creator**

- 作者 Devman

- Creator版本要求 v3.0.0

![resource-icon](Cocos-Creator如何使用Protobufjs-2-x-3-0/66cc590a725b2795c0d18709ffddfc04.png)

商店地址：https://store.cocos.com/app/detail/2404

笔者购买了这个插件，使用起来很方便，作者也专门为这个插件使用录制了一个教程

教程地址：[【Cocos引擎插件分享】protobuf-creator](https://www.bilibili.com/video/BV1J54y1Y7KH)

### 官方教程

#### 使用npm方案使用protobuf

示例：[通过 npm 使用 protobuf.js](https://docs.cocos.com/creator/3.0/manual/zh/scripting/modules/example-protobufjs.html)

[Cocos Creator 3.0 里如何玩转 npm 海量资源](https://mp.weixin.qq.com/s/QFElmvZY7S2Iw3SXa7kwfw)

官方介绍了如何使用npm 方式和Protobuf结合，教程很详细，但是步骤很多，笔者没有实践，但是出于对官方人员对信任，相信这个方案也是可以正常使用的。

### 笔者自己结合网友的综合方案

### Protobuf介绍

![image-20210401202044403](Cocos-Creator如何使用Protobufjs-2-x-3-0/image-20210401202044403.png)

直接看这篇文章即可：[Protocol Buffers3语言指南（译）](https://mp.weixin.qq.com/s/bs8oukuGMF6PcX6hNXuUbw)

#### Creator WebSocket Protobuf整合

![图片](Cocos-Creator如何使用Protobufjs-2-x-3-0/640.png)

[Creator WebSocket Protobuf整合之保姆级全攻略](https://mp.weixin.qq.com/s/8KA6wYdFLWnaLfvgf82k7g)

文章 作者文章源码：[点击进入](https://github.com/fanfq/creator_ws_protobuf_client)  作者博客：[点击访问]()

#### 笔者结合后示例

**Cocos Creator 2.x 示例地址**：[点击进入](https://github.com/jsroads/mylibs/tree/main/Protobufjs/Cocos%20Creator%202.x/%E9%80%9A%E4%BF%A1%E5%8D%8F%E8%AE%AE)

**Cocos Creator 3.0 示例地址**：[点击进入](https://github.com/jsroads/mylibs/tree/main/Protobufjs/Cocos%20Creator%203.0/%E9%80%9A%E4%BF%A1%E5%8D%8F%E8%AE%AE)

##### 使用方法：

1. 直接运行 `setup_clent.sh`

```shell
smile@bogon 通信协议 % /Users/smile/Downloads/Cocos\ Creator\ Protobuf\ 协议/Cocos\ Creator\ 3.0/技术/通信协议/setup_clent.sh
['copyjs.py']
找到--语法是--ES5
替换全局对象 global
删除导出模块 mykj ！！
删除导出模块 $root ！！
------bufBundle.js 更换完毕-----
------bufBundle.d.ts 更换完毕-----
复制路径： /Users/smile/work/ccc/game
源目录： /Users/smile/Downloads/Cocos Creator Protobuf 协议/Cocos Creator 3.0/技术/通信协议/dist
filename bufBundle.js
复制路径 /Users/smile/Downloads/Cocos Creator Protobuf 协议/Cocos Creator 3.0/技术/通信协议/dist/bufBundle.js
源目录： /Users/smile/work/ccc/game/assets/protobuf/dist/bufBundle.js
filename bufBundle.d.ts
复制路径 /Users/smile/Downloads/Cocos Creator Protobuf 协议/Cocos Creator 3.0/技术/通信协议/dist/bufBundle.d.ts
源目录： /Users/smile/work/ccc/game/assets/protobuf/dist/bufBundle.d.ts
复制完毕！！
```

##### 原理解释：

1. 主要使用 `pbjs` 命令 生成 `js`文件

```shell
pbjs -t static-module -r creator3 -w commonjs --es6 --no-verify --force-number "./protobuf/*.proto" -o ./dist/bufBundle.js
```

2. 使用`pbts` 命令生成 声明文件`.d.ts`

```shell
pbts -m -o ./dist/bufBundle.d.ts ./dist/bufBundle.js
```

3. 使用 `python`（`python3`环境）修改构建后的 `js` 文件和`.d.ts`文件
4. 使用终端运行  `shell` 脚步运行`python`脚本

如果没有shell 环境的可以直接单独运行 1/2/3 即可。

**总的来说，开发者不能受制于插件，否则后面插件一旦停更，项目则会受影响。而笔者采用的这个方法，是脱离插件存在的，虽然运行界面丑陋了些，但是效果是一样的，当然如果自己能修改插件源码义工项目使用，自然再好不过**

## 参考

- [protobufjs 官网](https://protobufjs.github.io/protobuf.js/)
- [Protocol Buffers3语言指南（译）](https://mp.weixin.qq.com/s/bs8oukuGMF6PcX6hNXuUbw)
- [Creator WebSocket Protobuf整合之保姆级全攻略](https://mp.weixin.qq.com/s/8KA6wYdFLWnaLfvgf82k7g)
- [Cocos引擎插件分享 protobuf-creator](https://store.cocos.com/app/detail/2404)

