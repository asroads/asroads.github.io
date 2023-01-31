---
title: Cocos Creaor双地图移动缩放
comments: true
categories: game
tags:
  - Cocos
abbrlink: cc539a8
date: 2023-01-14 00:25:38
---

关于地图移动之前写过[地图缩放拖动组件](https://blog.asroads.com/post/ce78284b.html)和[CocosCreator摄像机移动以及缩放地图](http://blog.asroads.com/post/5f9d2fe9.html)关于地图移动和缩放的功能实现，最近遇到了一个新的稍微有点区别的改造版本，就是两张地图同时移动和缩放，这种操作在手机的找茬游戏里是一个常见的情景，于是乎，稍加改造，就做了效果，下面简单的记录一下
<!--more-->

### 最终效果

俗话说，一图胜千言，为了不浪费大家时间，咱们先看效果图：

![101](Cocos-Creaor%E5%8F%8C%E5%9C%B0%E5%9B%BE%E7%A7%BB%E5%8A%A8%E7%BC%A9%E6%94%BE/101.gif)

### 主要区别

这个主要是在[地图缩放拖动组件](https://blog.asroads.com/post/ce78284b.html)改造出来的，其中的一些操作这里不再详述，只说添加的部分吧！

![image-20230124003348691](Cocos-Creaor%E5%8F%8C%E5%9C%B0%E5%9B%BE%E7%A7%BB%E5%8A%A8%E7%BC%A9%E6%94%BE/image-20230124003348691.png)

![image-20230124003424068](Cocos-Creaor%E5%8F%8C%E5%9C%B0%E5%9B%BE%E7%A7%BB%E5%8A%A8%E7%BC%A9%E6%94%BE/image-20230124003424068.png)

### 原理

就是在移动其中一张地图的时候，派发一个自定义事件，然后对另外一张地图 进行位置和scale的同等变化。

核心代码：

```javascript
//.... 其他代码 省略
this.node.emit("touch_move_distance", targetPos,this.map.scale);
//.... 其他代码 省略
```

```javascript
 this.node.on("touch_move_distance", (targetPos: cc.Vec3,scale:number)=>{
            this.nodeList.forEach((value, index, array)=>{
                let targetNode:cc.Node = value.targetNode;
                let rate:number = value.rate;
                let pos: cc.Vec3 = targetPos.clone().multiplyScalar(rate);
                targetNode.scale = scale;
                targetNode.setPosition(pos.x, pos.y, pos.z);
            });
        });
```

最后 源码地址：[点击前往](https://github.com/jsroads/mylibs/blob/main/mapScaleDemo/doublemap/DoubleMapTouchController.ts)