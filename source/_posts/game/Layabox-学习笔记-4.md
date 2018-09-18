---
title: Layabox-学习笔记-(4)
date: 2018-08-17 20:45:33
categories: "game"
tags:
- Layabox
---

### Layabox 微信小游戏适配问题

最近开始用Layabox开发微信小游戏，小游戏出来差不多半年多了，前面的开发者基本把该踩的坑都踩的差不多了，站在巨人的肩膀上，就会走的很快，看的很远，所以我遇到的很多问题都是前辈们遇到的问题，有些问题，Layabox官方已经解决，有些问题还没来得及解决，所以我记录下，便于以后自己查阅。

<!-- more -->

### 微信小游戏，在IPhoneX机器上面适配问题

#### 问题描述：

- 主要是屏幕显示问题有误，但是触摸位置还是对的

#### 查找问题

- 首先去论坛看看别人怎么解决的，避免重复发明轮子
- 再次认真的读了一遍 官方关于适配的方案
- 利用百度、Google搜索问题
- 去知名论坛比如知乎、博客园搜索问题

#### 最后结论

在很多问题里面找到了三篇认为比较靠谱的

1. [微信小游戏中iPhoneX的适配问题](https://ask.layabox.com/question/15359)
2. [微信小游戏适配问题](https://ask.layabox.com/question/14023)
3. [微信小游戏屏幕适配](https://tianbozhang.coding.me/minigame-screenadaptation.html)

其中前两篇 官方人员回复得到了部分答案以下是原文摘抄

> 微信小游戏不允许引擎来控制canvas的大小。小游戏的canvas大小是自动拉伸的。而exactfit模式的stage宽高会一直等于设计宽高，当canvas无法控制的时候，exactfit的适配其实是失效了。这里建议你用fixedauto、或者full等无需考虑设计宽高，会自动改变stage宽高的适配模式，再结合一些其它适配方案来尝试一下。
>
> 比如，
> 就拿你上传的DEMO来讲，使用fixedauto、或者full适配模式后，将bg的top、bottom、left、right属性设置为0，不就可以铺满全屏了么。

------

> 以上问题解决方案，
> 1，小游戏用webGL模式不要用canvas模式。
> 2, webGL模式下，iphoneX显示确实是在部分适配模式下存在不准确的情况。核心原因是微信小游戏不允许引擎来控制canvas的大小。小游戏的canvas大小是自动拉伸的。所以有一些适配模式就失效了。这里建议采用fixedauto、或者full等模式，这些模式的适配原理是无需考虑设计宽高，会自动改变stage宽高。所以用这些适配模式可解决遇到的问题。如果存在背景图显示全屏铺满的需求，可以通过将背景图的top、bottom、left、right属性设置为0，就可以铺满全屏了。

第三篇也给出了相应的两种方案两种可行的解决方案

> ### 两套图适配
>
> iphonex 大分辨率与5s 小分辨率对比图见《游戏截图》文档
>
> ------
>
> 按物理分辨率取比，以16：9为基础
>
> ```typescript
> //比例大于16:9的，标记为大分辨率
> this.isLargeScreen= Math.floor(height/width)>Math.floor(16/9)
> ```
>
> 背景资源分别获取
>
> ```typescript
>  if(this.isLargeScreen) bg.skin = "大图"
>  
> else bg.skin = "小图";
> ```
>
> 不缩放(默认值，可不写)
>
> ```typescript
> Laya.stage.scaleMode = Laya.Stage.SCALE_NOSCALE
> ```
>
> 舞台尺寸设置为固定物理尺寸，保证各16:9 设备等比缩放，不会出现黑边typescripttypescript
>
> ```typescript
> if (this.isLargeScreen) {
>            Laya.init(1125, 2436, Laya.WebGL);
>        } else {
>            Laya.init(640, 1136, Laya.WebGL);
>        }
> ```
>
> 原因：现有缩放模式不支持两边裁剪，至多为一边裁剪

**纠正**我认为作者这个判断有问题`this.isLargeScreen= Math.floor(height/width)>Math.floor(16/9)`

应该是 `this.isLargeScreen= height/width>Math.floor16.0/9.0`

> ### 一套图适配
>
> 示例：[天才削熊猫](http://tcxxm.y2game.net/)
>
> **思路**：高度不变，宽度适配，水平居中，两边裁剪，元素相对布局，设计时设置安全区域
>
> 实现：一套背景图，`1480x1920`，水平居中：centerx = 0，y = 0，启用相对父类布局`layoutEnable = true`
>
> 屏幕宽高：`1080x1920`
>
> 代码或UI 元素：启用相对父类布局，设置left,right top ,bottom，`layoutEnable = true`
>
> 缩放模式：`fixedHeight`
>
> 代码示例
>
> 以`1080x1920`(具体看需求)尺寸图为标准、设计尺寸，路径以res为例
>
> ```typescript
> //舞台初始化
> Laya.init(1080, 1920);
> //缩放模式
> Laya.stage.scaleMode = Laya.Stage.SCALE_FIXED_HEIGHT;
> //背景添加
> var imageBg:Laya.Image = new Laya.Image();  
> imageBg.skin = "res/bg_main.png";
> imageBg.centerX = 0;
> imageBg.layoutEnabled = true;
> imageBg.y = 0;
> Laya.stage.addChild(imageBg);
> //UI元素添加，以添加到舞台为例
>  var btn: Laya.Button = new Laya.Button();
> btn.skin = "res/btn.png";
> btn.layoutEnabled = true;
> btn.left = 165;
> btn.bottom = 50;
> btn.stateNum = 1;
> Laya.stage.addChild(btn);
> ```
>
>

最后我自己的方案是先根据当前屏幕大小判断机型，然后去做处理

```typescript
module asroads{
    import Size = Laya.Size;
    export class ConfigGame {
        private static _size:Size;
        private static _width:number;
        private static _height:number;
        static get height(): number {
            return ConfigGame.size.height;
        }
        static get width(): number {
            return ConfigGame.size.width;
        }
        static get  size(): Laya.Size {
            if(ConfigGame.isLargeScreen())
                this._size = {width:640,height:1386};
            else
                this._size = {width:640,height:1136};
            return this._size;
        }
        public static isLargeScreen():boolean{
            return (Laya.Browser.clientHeight/Laya.Browser.clientWidth)>(16.0/9.0)?true:false;
        }
    }
}
```

然后结合 LayaAir 自身带的布局属性，基本解决了问题。

### 参考资料

1. [微信小游戏中iPhoneX的适配问题](https://ask.layabox.com/question/15359)
2. [微信小游戏适配问题](https://ask.layabox.com/question/14023)
3. [微信小游戏屏幕适配](https://tianbozhang.coding.me/minigame-screenadaptation.html)



