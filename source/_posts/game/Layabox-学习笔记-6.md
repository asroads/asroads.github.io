---
title: Layabox-学习笔记-(6)
categories: game
tags:
  - Layabox
abbrlink: 13dab74
date: 2018-08-26 14:32:33
---

### LayaBox怎么在加载游戏的时候添加进度条

游戏做的差不多了，就想做个加载进度条，这样显示游戏当前正在加载，给用户一个号的反馈。

看了官方的API 也去论坛搜索了教程，最后决定自己写一个。

<!-- more -->

- 参考页面  [ProgressBar](http://layaair.ldc.layabox.com/api/?category=UI&class=laya.ui.ProgressBar)
- 参考页面 [LoaderManager](http://layaair.ldc.layabox.com/api/?category=Core&class=laya.net.LoaderManager)（注意，Laya.loader的类型是[laya.net.LoaderManager](javascript:void(0))）

我们看到

> | load | ()   | method |      |
> | ---- | ---- | ------ | ---- |
> |      |      |        |      |
>
> ```
> public function load(url:*, complete:Handler = null, progress:Handler = null, type:String = null, priority:int = 1, cache:Boolean = true, group:String = null, ignoreCache:Boolean = false):LoaderManager
> ```
>
>
>
>
>
>
>
> 加载资源。资源加载错误时，本对象会派发 Event.ERROR 事件，事件回调参数值为加载出错的资源地址。
>
> 因为返回值为 LoaderManager 对象本身，所以可以使用如下语法：Laya.loader.load(...).load(...);
>
>
>
> Parameters
>
> |      | `url:*` — 要加载的单个资源地址或资源信息数组。比如：简单数组：["a.png","b.png"]；复杂数组[{url:"a.png",type:Loader.IMAGE,size:100,priority:1},{url:"b.json",type:Loader.JSON,size:50,priority:1}]。 |
> | ---- | ------------------------------------------------------------ |
> |      |                                                              |
> |      | `complete:Handler` (default = `null`)`` — 加载结束回调。根据url类型不同分为2种情况：1. url为String类型，也就是单个资源地址，如果加载成功，则回调参数值为加载完成的资源，否则为null；2. url为数组类型，指定了一组要加载的资源，如果全部加载成功，则回调参数值为true，否则为false。 |
> |      |                                                              |
> |      | `progress:Handler` (default = `null`)`` — 加载进度回调。回调参数值为当前资源的加载进度信息(0-1)。 |
> |      |                                                              |
> |      | `type:String` (default = `null`)`` — 资源类型。比如：Loader.IMAGE。 |
> |      |                                                              |
> |      | `priority:int` (default = `1`)`` — (default = 1)加载的优先级，优先级高的优先加载。有0-4共5个优先级，0最高，4最低。 |
> |      |                                                              |
> |      | `cache:Boolean` (default = `true`)`` — 是否缓存加载结果。    |
> |      |                                                              |
> |      | `group:String` (default = `null`)`` — 分组，方便对资源进行管理。 |
> |      |                                                              |
> |      | `ignoreCache:Boolean` (default = `false`)`` — 是否忽略缓存，强制重新加载。 |
>
>
>
>
>
> Returns
>
> |      | `LoaderManager` — 此 LoaderManager 对象本身。 |
> | ---- | --------------------------------------------- |
> |      |                                               |

我们发现 第三个参数 是 加载进度的，然后我们结合官方的[demo](http://layaair.ldc.layabox.com/demo/?category=2d&group=UI&name=ProgressBar)展示 

```typescript
module laya {
    import Stage = Laya.Stage;
    import ProgressBar = Laya.ProgressBar;
    import Handler = Laya.Handler;
    import WebGL = Laya.WebGL;

    export class UI_ProgressBar {
        private progressBar: ProgressBar;

        constructor() {
            // 不支持WebGL时自动切换至Canvas
            Laya.init(800, 600, WebGL);

            Laya.stage.alignV = Stage.ALIGN_MIDDLE;
            Laya.stage.alignH = Stage.ALIGN_CENTER;

            Laya.stage.scaleMode = Stage.SCALE_SHOWALL;
            Laya.stage.bgColor = "#232628";

            Laya.loader.load(["../../res/ui/progressBar.png", "../../res/ui/progressBar$bar.png"], Handler.create(this, this.onLoadComplete));
        }

        private onLoadComplete(): void {
            this.progressBar = new ProgressBar("../../res/ui/progressBar.png");

            this.progressBar.width = 400;

            this.progressBar.x = (Laya.stage.width - this.progressBar.width) / 2;
            this.progressBar.y = Laya.stage.height / 2;

            this.progressBar.sizeGrid = "5,5,5,5";
            this.progressBar.changeHandler = new Handler(this, this.onChange);
            Laya.stage.addChild(this.progressBar);

            Laya.timer.loop(100, this, this.changeValue);
        }

        private changeValue(): void {

            if (this.progressBar.value >= 1)
                this.progressBar.value = 0;
            this.progressBar.value += 0.05;
        }

        private onChange(value: number): void {
            console.log("进度：" + Math.floor(value * 100) + "%");
        }
    }
}
new laya.UI_ProgressBar();
```

最后 自己做了一个 代码如下

```typescript
/**
 * Created by asroads on 2018/8/22 上午10:05
 * Note: 加载进度条 使用方法 MyGameLoading.instance.show();
 * 如果加载报错  MyGameLoading.instance.hide();
 */
module smile {
    import ProgressBar = Laya.ProgressBar;
    import Handler = Laya.Handler;
    export class MyGameLoading {
        private progressBar: ProgressBar;
        private static _instance: GameLoading;
        constructor() {
        }
        public show():Handler{
            if(!this.progressBar){
                this.initProgressBar();
            }
            this.progressBar.value = 0;
            this.progressBar.changeHandler = Handler.create(this,this.onLoadprogress,null, false);
            Laya.stage.addChild(this.progressBar);
            return this.progressBar.changeHandler;
        }
        public hide() {
            this.progressBar.changeHandler.recover();
            this.progressBar.destroy();
            console.log("hide-------:" + this.progressBar);
        }
        private onLoadprogress(progress: number): void {
            console.log("加载进度: " + progress);
            console.log("进度：" + Math.floor(progress * 100) + "%");
            this.changeValue(progress);
        }
        private changeValue(progress): void {
            if (this.progressBar.value >= 1) {
                this.hide();
                this.progressBar.value = 1;
                return;
            }
            this.progressBar.value  = progress;
        }

        private initProgressBar() {
            if (!this.progressBar) this.progressBar = new ProgressBar("comp/progress_load.png");
            this.progressBar.width = 400;
            this.progressBar.x = (Laya.stage.width - this.progressBar.width) / 2;
            this.progressBar.y = Laya.stage.height / 2;
            this.progressBar.sizeGrid = "5,5,5,5";
            this.progressBar.zOrder = 1000;
        }
        static get instance(): hoolai.GameLoading {
            if (!this._instance) this._instance = new GameLoading();
            return this._instance;
        }
    }
}
```

搞定 引用的时候 `MyGameLoading.instance.show();`如果担心自己素材加载失败 可以在自己的加载失败里写上这个`MyGameLoading.instance.hide();`