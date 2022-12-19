---
title: CocosCreator微信小游戏接入原生模板(格子广告)
comments: true
categories: game
tags:
  - Cocos
  - 小游戏
abbrlink: 7f706bee
date: 2022-12-16 00:45:45
---

最近接到一个任务就是微信小游戏内格子广告的接入，之前接入的广告大多都是激励视频广告和Banner广告，这次这个原生模板的格子广告，一下子没有找到好的借鉴的文章和参考例子，于是折腾了半天才终于搞定，下面就记录一下接入的步骤和注意事项，便于以后有问题的时候参考。
<!--more-->
首先最好的文档就是官方文档，于是乎找到这么几个链接：

- [微信广告-广告组件](https://ad.weixin.qq.com/docs/49)
- [小游戏-原生模板广告](https://developers.weixin.qq.com/minigame/dev/guide/open-ability/ad/custom-ad.html)
- [wx.createCustomAd  API](https://developers.weixin.qq.com/minigame/dev/api/ad/wx.createCustomAd.html)

看完上面几个链接后，下面说说相关知识：

## 模板广告介绍

1. 所谓模板广告，就是已经规定了相关的尺寸 规范等，开发者只能调用，其实就是一个带更多功能的 banner广告
2. 格子广告有几行几列的，这个根据自己的需求，我接入的是两种  1X5 格子 和 5 X5 格子
3. 1X5 格子 类似banner，这里也是居中底部对齐
4. 5X5 格子，类似插屏广告，这里尽量居中对齐

## 步骤

1. 首先去游戏管理中心，注册自己的原生模板类型
2. 然后开始按照API去调用
3. 我这里发现 模板广告如果类型不同，可以创建多个，所以可以用多个对象存储，显示的时候，调用show 方法即可

具体不再赘述，下面直接给出代码：

`1X5`原生模板格子 `WxCustomAd.ts`

```typescript
export class WxCustomAd {
    //当前已实例的CustomAd广告状态
    static showStatus: boolean = false;
    static loadStatus: boolean = false;
    //当前已实例的CustomAd广告
    static cache: WechatMinigame.CustomAd = null;
    //当前使用的广告id
    static curAdUnitId: string = "";
    //设备可使用窗口宽度
    static windowWidth: number;
    //设备可使用窗口高度
    static windowHeight: number;
    //设备像素比
    static pixelRatio: number;
    //设备像素比
    static adPixelRatio: number = 0.9;

    private static style: WechatMinigame.CustomAdStyle = null;


    static getStyle(): WechatMinigame.CustomAdStyle {
        if (!WxCustomAd.style) {
            let ratio = WxCustomAd.windowWidth/360;

            WxCustomAd.style = {
                top: (WxCustomAd.windowHeight - 106 * WxCustomAd.adPixelRatio),
                left: (WxCustomAd.windowWidth - 360 * WxCustomAd.adPixelRatio ) * 0.5,
                width: 360 * WxCustomAd.adPixelRatio,
                fixed: false
            };
            //设置开启状态
            WxCustomAd.showStatus = true;
        }
        console.log("WxCustomAd.style", JSON.stringify(WxCustomAd.style));
        return WxCustomAd.style;
    }

    //初始化CustomAd广告
    static init(obj: {adUnitId:string} = {adUnitId:""}): Promise<void> {
        return new Promise((resolve, reject) => {
            //初始化单位转化
            let systemInfo: any = wx.getSystemInfoSync();
            WxCustomAd.windowHeight = systemInfo.windowHeight;
            WxCustomAd.windowWidth = systemInfo.windowWidth;
            WxCustomAd.pixelRatio = systemInfo.pixelRatio;
            //设置adUnitId列表
            if (obj.adUnitId) {
                WxCustomAd.curAdUnitId = obj.adUnitId;
            }
            //初始化刷新定时
            WxCustomAd.load().then((t) => {
                console.log("原生模板广告加载耗时", new Date().getTime() - t, "ms");
                resolve()
            }).catch(err => {
                console.log(err)
                reject();
            });
        })
    }

    //加载CustomAd广告
    static load(): Promise<any> {
        return new Promise((resolve, reject) => {
            const t = new Date().getTime();
            const style: any = WxCustomAd.getStyle();
            const adUnitId: any = WxCustomAd.curAdUnitId;
            WxCustomAd.cache && WxCustomAd.cache.destroy();
            WxCustomAd.cache = wx.createCustomAd({adUnitId: adUnitId, style: style, adIntervals: 30});
            WxCustomAd.loadStatus = false;
            WxCustomAd.cache.onLoad(WxCustomAd.onLoad);
            WxCustomAd.cache.onError(WxCustomAd.onError);
            WxCustomAd.cache.onHide(WxCustomAd.onHide);
            WxCustomAd.cache.onClose(WxCustomAd.onClose);
            resolve(t);
        })
    }

    //显示CustomAd广告
    static show(options: any = {}, end: Function = null): void {
        //设置开启状态
        WxCustomAd.showStatus = true;
        if (options.adUnitId) {
            WxCustomAd.init(options).then(() => {
                WxCustomAd.show({}, end);
            });
        } else if (options.realtime) {
            WxCustomAd.load().then((t) => {
                console.log("原生模板广告加载耗时", new Date().getTime() - t, "ms");
                WxCustomAd.show({}, end);
            }).catch(err => console.log(err));
        } else {
            const t = new Date().getTime();
            WxCustomAd.cache.show().then(() => {
                console.log("原生模板广告显示成功耗时", new Date().getTime() - t, "ms");
                end && end(true);
            }).catch(err => {
                console.log(err)
                end && end(false);
            });
        }
    }

    //隐藏CustomAd广告
    static hide(): void {
        //隐藏CustomAd广告
        WxCustomAd.showStatus = false;
        WxCustomAd.loadStatus = false;
        if (WxCustomAd.cache && WxCustomAd.cache.isShow()) WxCustomAd.cache.hide().then(() => console.log('原生模板广告隐藏!!'));
    }

    //销毁CustomAd广告
    static destroy(): void {
        //销毁CustomAd广告
        if (WxCustomAd.cache) WxCustomAd.cache.destroy();
        // WxCustomAd.cache = null;
        // //设置开启状态
        // WxCustomAd.showStatus = false;
        // WxCustomAd.loadStatus = false;
    }


    //监听CustomAd广告加载事件
    static onLoad(): void {
        console.log('原生模板广告加载成功');
        WxCustomAd.cache.offLoad(WxCustomAd.onLoad);
        if (WxCustomAd.showStatus === false) {
            WxCustomAd.hide();
            return;
        }else {
            WxCustomAd.loadStatus = true;
            WxCustomAd.showStatus = true;
        }
        // WxCustomAd.cache.show();
    }

    //监听CustomAd广告加载事件
    static onClose(): void {
        console.log('原生模板广告关闭')
        WxCustomAd.cache.offClose(WxCustomAd.onClose);
        WxCustomAd.cache.offHide(WxCustomAd.onHide);
        WxCustomAd.cache.offError(WxCustomAd.onError);
        // WxCustomAd.cache.destroy();
        WxCustomAd.cache = null;
        //设置开启状态
        WxCustomAd.showStatus = false;
        WxCustomAd.loadStatus = false;
    }

    static onHide(): void {
        console.log('原生模板广告隐藏');
        // WxCustomAd.cache.offHide(WxCustomAd.onHide);
        //设置开启状态
        WxCustomAd.showStatus = false;
        WxCustomAd.loadStatus = false;
    }

    //监听CustomAd广告错误事件
    static onError(res: any): void {
        console.error("原生模板广告加载错误", res);
        WxCustomAd.cache.offError(WxCustomAd.onError);
        switch (res.errCode) {
            case 1002://广告单元无效
            case 1005://广告组件审核中
            case 1006://广告组件被驳回
            case 1007://广告组件被封禁
            case 1008://广告单元已关闭
                break;
            case 1000://后端接口调用失败
            case 1001://参数错误
            case 1003://内部错误
            case 1004://无合适的广告
            default:
                setTimeout(() => {
                    WxCustomAd.load();
                }, 3000);
                break;
        }
    }
}
```

`5X5`格子 `WxCustomBigAd.ts`

```typescript
export class WxCustomBigAd {
    //当前已实例的CustomAd广告状态
    static showStatus: boolean = false;
    static loadStatus: boolean = false;
    //当前已实例的CustomAd广告
    static cache: WechatMinigame.CustomAd = null;
    //当前使用的广告id
    static curAdUnitId: string = "";
    //设备可使用窗口宽度
    static windowWidth: number;
    //设备可使用窗口高度
    static windowHeight: number;
    //设备像素比
    static pixelRatio: number;

    private static style: WechatMinigame.CustomAdStyle = null;

    static getStyle(): WechatMinigame.CustomAdStyle {
        if (!WxCustomBigAd.style) {
            let width:number = WxCustomBigAd.windowWidth;
            width = Math.max(width,300);
            const left:number = (WxCustomBigAd.windowWidth -width) * 0.5
            WxCustomBigAd.style = {
                top: (WxCustomBigAd.windowHeight - width *1.25 * 1.2) * 0.5,
                left:left,
                width: width,
                fixed: false
            };
            //设置开启状态
            WxCustomBigAd.showStatus = true;
        }
        console.log("WxCustomBigAd.style", JSON.stringify(WxCustomBigAd.style));
        return WxCustomBigAd.style;
    }

    //初始化CustomAd广告
    static init(obj: {adUnitId:string} = {adUnitId:""}): Promise<void> {
        return new Promise((resolve, reject) => {
            //初始化单位转化
            let systemInfo: any = wx.getSystemInfoSync();
            console.log("systemInfo", JSON.stringify(systemInfo, null, 2));
            WxCustomBigAd.windowHeight = systemInfo.windowHeight;
            WxCustomBigAd.windowWidth = systemInfo.windowWidth;
            WxCustomBigAd.pixelRatio = systemInfo.pixelRatio;
            //设置adUnitId列表
            if (obj.adUnitId) {
                WxCustomBigAd.curAdUnitId = obj.adUnitId;
            }
            //初始化刷新定时
            WxCustomBigAd.load().then((t) => {
                console.log("原生模板广告加载耗时", new Date().getTime() - t, "ms");
                resolve()
            }).catch(err => {
                console.log(err)
                reject();
            });
        })
    }

    //加载CustomAd广告
    static load(): Promise<any> {
        return new Promise((resolve, reject) => {
            const t = new Date().getTime();
            const style: any = WxCustomBigAd.getStyle();
            const adUnitId: any = WxCustomBigAd.curAdUnitId;
            WxCustomBigAd.cache && WxCustomBigAd.cache.destroy();
            WxCustomBigAd.cache = wx.createCustomAd({adUnitId: adUnitId, style: style, adIntervals: 30});
            WxCustomBigAd.loadStatus = false;
            WxCustomBigAd.cache.onLoad(WxCustomBigAd.onLoad);
            WxCustomBigAd.cache.onError(WxCustomBigAd.onError);
            WxCustomBigAd.cache.onHide(WxCustomBigAd.onHide);
            WxCustomBigAd.cache.onClose(WxCustomBigAd.onClose);
            resolve(t);
        })
    }

    //显示CustomAd广告
    static show(options: any = {}, end: Function = null): void {
        //设置开启状态
        WxCustomBigAd.showStatus = true;
        if (options.adUnitId) {
            WxCustomBigAd.init(options).then(() => {
                WxCustomBigAd.show({}, end);
            });
        } else if (options.realtime) {
            WxCustomBigAd.load().then((t) => {
                console.log("原生模板广告加载耗时", new Date().getTime() - t, "ms");
                WxCustomBigAd.show({}, end);
            }).catch(err => console.log(err));
        } else {
            const t = new Date().getTime();
            WxCustomBigAd.cache.show().then(() => {
                console.log("原生模板广告显示成功耗时", new Date().getTime() - t, "ms");
                end && end(true);
            }).catch(err => {
                console.log(err)
                end && end(false);
            });
        }
    }

    //隐藏CustomAd广告
    static hide(): void {
        //隐藏CustomAd广告
        WxCustomBigAd.showStatus = false;
        WxCustomBigAd.loadStatus = false;
        if (WxCustomBigAd.cache&& WxCustomBigAd.cache.isShow()) WxCustomBigAd.cache.hide().then(() => console.log('原生模板广告隐藏!!'));
    }

    //销毁CustomAd广告
    static destroy(): void {
        //销毁CustomAd广告
        if (WxCustomBigAd.cache) WxCustomBigAd.cache.destroy();
        // WxCustomBigAd.cache = null;
        // //设置开启状态
        // WxCustomBigAd.showStatus = false;
        // WxCustomBigAd.loadStatus = false;
    }


    //监听CustomAd广告加载事件
    static onLoad(): void {
        console.log('原生模板广告加载成功');
        WxCustomBigAd.cache.offLoad(WxCustomBigAd.onLoad);
        if (WxCustomBigAd.showStatus === false) {
            WxCustomBigAd.hide();
            return;
        }else {
            WxCustomBigAd.loadStatus = true;
            WxCustomBigAd.showStatus = true;
        }
        // WxCustomBigAd.cache.show();
    }

    //监听CustomAd广告加载事件
    static onClose(): void {
        console.log('原生模板广告关闭')
        WxCustomBigAd.cache.offClose(WxCustomBigAd.onClose);
        WxCustomBigAd.cache.offHide(WxCustomBigAd.onHide);
        WxCustomBigAd.cache.offError(WxCustomBigAd.onError);
        // WxCustomBigAd.cache.destroy();
        WxCustomBigAd.cache = null;
        //设置开启状态
        WxCustomBigAd.showStatus = false;
        WxCustomBigAd.loadStatus = false;
    }

    static onHide(): void {
        console.log('原生模板广告隐藏');
        // WxCustomBigAd.cache.offHide(WxCustomBigAd.onHide);
        //设置开启状态
        WxCustomBigAd.showStatus = false;
        WxCustomBigAd.loadStatus = false;
    }

    //监听CustomAd广告错误事件
    static onError(res: any): void {
        console.error("原生模板广告加载错误", res);
        WxCustomBigAd.cache.offError(WxCustomBigAd.onError);
        switch (res.errCode) {
            case 1002://广告单元无效
            case 1005://广告组件审核中
            case 1006://广告组件被驳回
            case 1007://广告组件被封禁
            case 1008://广告单元已关闭
                break;
            case 1000://后端接口调用失败
            case 1001://参数错误
            case 1003://内部错误
            case 1004://无合适的广告
            default:
                setTimeout(() => {
                    WxCustomBigAd.load();
                }, 3000);
                break;
        }
    }
}
```

顺便给出`banner` 广告的 `WxBannerAd.ts`

```typescript
export  class WxBannerAd {
    //当前已实例的Banner广告状态
    static showStatus: boolean = false;
    static loadStatus: boolean = false;
    //当前已实例的Banner广告
    static cache: any = null;
    static _onResize: Function = null;
    static _onLoad: Function = null;
    static _onError: Function = null;
    //是否开启自动切换广告
    static timerStatus: boolean = false;
    //定时器实例
    static timer: any;
    //自动切换的时间间隔(单位：s)
    static interval: number = 600000;
    static intervalMin: number = 60;
    //是否为自动刷新
    static isTimerLoad: boolean = false;
    //更新的秒数
    static timerSecond: number = 0;
    //当前使用的广告id
    static curAdUnitId: string = null;
    //有效的广告id列表
    static adUnitIds: string[] = [];
    //已经被检验失效的广告id
    static adUnitIdsErr: string[] = [];
    //+----------------------------------------------------------------------------------------------------
    //| Banner广告刷新机制
    //+----------------------------------------------------------------------------------------------------
    //每一个广告id最大的失败数
    static adUnitIdMaxFailNum: number = 3;
    //不同广告id的使用状态 格式:{广告ID:状态(false表示广告ID不可用、true表示广告ID可用)}
    static adUnitIdStatus: any = {};
    //不同广告id的加载失败次数 格式:{广告ID:失败次数}
    static adUnitIdFailNum: any = {};
    //当前手机型
    static curModel: string = "";
    //需要做上移操作的手机型号
    static modelList: string[] = ["iPhone X"];
    //设备可使用窗口宽度
    static windowWidth: number;
    //+----------------------------------------------------------------------------------------------------
    //| Banner广告ID处理
    //+----------------------------------------------------------------------------------------------------
    //设备可使用窗口高度
    static windowHeight: number;
    //游戏场景宽度
    static stageWidth: number;
    //游戏场景高度
    static stageHeight: number;

    static deltaY: number = 0;
    //布局样式模式列表
    static styleModeList: string[] = [
        "left-top",//左上角布局
        "center-top",//顶部居中布局
        "right-top",//右上角布局
        "left-bottom",//左下角布局
        "center-bottom",//底部居中布局[默认值]
        "right-bottom",//右下角布局
        "top",//顶部居中布局
        "bottom",//底部居中布局
    ];
    //布局样式模式 none使用自定义样式 left-bottom左下角布局 center-bottom/bottom底部居中布局[默认值] right-bottom右下角布局
    static styleMode: string = "center-bottom";
    //自定义布局样式 （样式规则：有bottom则top无效，有right则left无效，有ceter则left与right无效）
    static styleCustom: any = {};
    //布局样式缓存(上一次show时的样式)
    static styleCache: any = {};
    //布局样式是否使用bottom布局
    static styleHasBottom: boolean = false;//如果styleHasBottom=true的情况，当下一次onLoad完成之后将会进行Banner广告Bottom校验
    static styleBottomNum: number = 0;//使用的单位类型为“可使用窗口高度”

    //+----------------------------------------------------------------------------------------------------
    //| 布局样式处理
    //+----------------------------------------------------------------------------------------------------

    //构造函数
    constructor() {

    }

    //布局样式整理
    static get style(): any {
        WxBannerAd.styleHasBottom = false;
        WxBannerAd.styleBottomNum = 0;
        let style: any = {};
        //设置宽度
        style.width = WxBannerAd.styleCustom.width ? WxBannerAd.stage2windowWidth(WxBannerAd.styleCustom.width) : ((WxBannerAd.windowHeight / WxBannerAd.windowWidth > 1.8) ? WxBannerAd.windowWidth : 300);
        if (style.width > WxBannerAd.windowWidth) style.width = WxBannerAd.windowWidth;
        //设置位置
        switch (WxBannerAd.styleMode) {
            case "none":
                if (WxBannerAd.styleCustom.bottom !== undefined) {
                    WxBannerAd.styleHasBottom = true;
                    WxBannerAd.styleBottomNum = WxBannerAd.stage2windowHeight(WxBannerAd.styleCustom.bottom);
                    style.top = WxBannerAd.windowHeight - style.width / 3 - WxBannerAd.styleBottomNum;
                } else if (WxBannerAd.styleCustom.top !== undefined) {
                    style.top = WxBannerAd.stage2windowHeight(WxBannerAd.styleCustom.top);
                } else {
                    WxBannerAd.styleHasBottom = true;
                    style.top = WxBannerAd.windowHeight - style.width / 3;
                }
                if (WxBannerAd.styleCustom.center !== undefined) {
                    style.left = WxBannerAd.windowWidth / 2 + WxBannerAd.stage2windowWidth(WxBannerAd.styleCustom.center) - style.width / 2;
                    if (style.left < 0) {
                        style.left = 0;
                    } else if (style.left > WxBannerAd.windowWidth - style.width) {
                        style.left = WxBannerAd.windowWidth - style.width;
                    }
                } else if (WxBannerAd.styleCustom.right !== undefined) {
                    style.left = WxBannerAd.windowWidth - WxBannerAd.stage2windowWidth(WxBannerAd.styleCustom.right) - style.width;
                } else if (WxBannerAd.styleCustom.left !== undefined) {
                    style.left = WxBannerAd.stage2windowWidth(WxBannerAd.styleCustom.left);
                } else {
                    style.left = (WxBannerAd.windowWidth - style.width) / 2;
                }
                break;
            case "left-top":
                style.top = 0;
                style.left = 0;
                break;
            case "right-top":
                style.top = 0;
                style.left = WxBannerAd.windowWidth - style.width;
            case "top":
            case "center-top":
                style.top = 0;
                style.left = (WxBannerAd.windowWidth - style.width) / 2;
                break;
            case "left-bottom":
                WxBannerAd.styleHasBottom = true;
                style.top = WxBannerAd.windowHeight - style.width / 3;
                style.left = 0;
                break;
            case "right-bottom":
                WxBannerAd.styleHasBottom = true;
                style.top = WxBannerAd.windowHeight - style.width / 3;
                style.left = WxBannerAd.windowWidth - style.width;
                break;
            case "bottom":
            case "center-bottom":
            default:
                WxBannerAd.styleHasBottom = true;
                style.top = WxBannerAd.windowHeight - style.width / 3 + WxBannerAd.deltaY;
                style.left = (WxBannerAd.windowWidth - style.width) / 2;
                break;
        }
        //缓存本次
        WxBannerAd.styleCache = {
            style,
            hasBottom: WxBannerAd.styleHasBottom,
            bottomNum: WxBannerAd.styleBottomNum,
        };
        console.log("style",style)
        return style;
    }

    //初始化Banner广告
    static init(obj: any = {}): void {
        //初始化单位转化
        let systemInfo: any = wx.getSystemInfoSync();
        WxBannerAd.curModel = systemInfo.model;
        WxBannerAd.windowHeight = systemInfo.windowHeight;
        WxBannerAd.windowWidth = systemInfo.windowWidth;
        WxBannerAd.deltaY = WxBannerAd.windowHeight - systemInfo.safeArea.bottom;
        console.log("WxBannerAd.deltaY",WxBannerAd.deltaY)
        WxBannerAd.stageHeight = cc.view.getFrameSize().height;
        WxBannerAd.stageWidth = cc.view.getFrameSize().width;

        //设置adUnitId列表
        if (obj.adUnitId) {
            //console.log("obj.adUnitId", obj.adUnitId);
            WxBannerAd.adUnitIds = obj.adUnitId;
            if (WxBannerAd.adUnitIds.length) {
                for (let v of WxBannerAd.adUnitIds) {
                    WxBannerAd.adUnitIdFailNum[v] = 0;
                    WxBannerAd.adUnitIdStatus[v] = true;
                }
                WxBannerAd.curAdUnitId = WxBannerAd.randomAdUnitId();
            }
        }
        //设置Banner广告加载失败判定为id失效的最大请求失败数
        if (obj.adUnitIdMaxFailNum) WxBannerAd.adUnitIdFailNum = obj.adUnitIdMaxFailNum;
        //设置布局样式自定义样式
        if (obj.style) {
            WxBannerAd.styleCustom = obj.style;
            //设置布局样式模式
            if (obj.style.styleMode && WxBannerAd.styleModeList.indexOf(obj.style.styleMode) !== -1) WxBannerAd.styleMode = obj.style.styleMode;
        }
        //设置刷新定时器时间间隔
        if (obj.interval) {
            WxBannerAd.interval = obj.interval;
            if (WxBannerAd.interval < WxBannerAd.intervalMin) WxBannerAd.interval = WxBannerAd.intervalMin;
        }
        //设置刷新定时器开启状态
        if (obj.timerStatus) WxBannerAd.timerStatus = obj.timerStatus;
        //初始化刷新定时
        WxBannerAd.load();
        if (WxBannerAd.timer) clearInterval(WxBannerAd.timer);
        WxBannerAd.timer = setInterval(() => {
            if (WxBannerAd.timerStatus && WxBannerAd.showStatus) {
                WxBannerAd.timerSecond++;
                if (WxBannerAd.timerSecond > WxBannerAd.interval) {
                    WxBannerAd.isTimerLoad = true;
                    WxBannerAd.load();
                }
            }
        }, 1000);
    }

    //加载banner广告
    static load(): void {
        if (WxBannerAd.cache) WxBannerAd.cache.destroy();
        let style: any;
        if (WxBannerAd.isTimerLoad) {
            style = WxBannerAd.styleCache.style;
            WxBannerAd.styleHasBottom = WxBannerAd.styleCache.hasBottom;
            WxBannerAd.styleBottomNum = WxBannerAd.styleCache.bottomNum;
            WxBannerAd.isTimerLoad = false;
        } else {
            style = WxBannerAd.style;
        }
        WxBannerAd.cache && (WxBannerAd.cache.destroy());
        WxBannerAd.cache = wx.createBannerAd({adUnitId: WxBannerAd.randomAdUnitId(), style: style,adIntervals: 30});
        WxBannerAd.cache.onLoad(WxBannerAd.onLoad);
        WxBannerAd.cache.onError(WxBannerAd.onError);
        WxBannerAd.cache.onResize(WxBannerAd.onResize);
        WxBannerAd.timerSecond = 0;
    }

    //显示banner广告
    static show(options: any = {}, end: Function = null): void {
        //设置布局样式自定义样式
        if (options.style) {
            WxBannerAd.styleCustom = options.style;
            //设置布局样式模式
            if (options.style.styleMode && WxBannerAd.styleModeList.indexOf(options.style.styleMode) !== -1) WxBannerAd.styleMode = options.style.styleMode;
        }
        //设置定时刷新的状态
        if (options.timerStatus) WxBannerAd.timerStatus = options.timerStatus;
        //是否实时更新
        if (options.realtime) {
            WxBannerAd.load();
            if (end) WxBannerAd._onLoad = () => {
                WxBannerAd.cache.show();
                end();
            }
        } else {
            if (options.style && options.style.width && WxBannerAd.stage2windowWidth(options.style.width) !== WxBannerAd.cache.style.width)
                WxBannerAd.onResize(WxBannerAd.onResize);
            let style = WxBannerAd.style;
            WxBannerAd.cache.style.width = style.width;
            WxBannerAd.cache.style.top = style.top;
            WxBannerAd.cache.style.left = style.left;

            WxBannerAd.cache.show();
            if (end) end();
        }
        //设置开启状态
        WxBannerAd.showStatus = true;
    }

    //隐藏banner广告
    static hide(): void {
        //隐藏Banner广告
        if (WxBannerAd.cache) WxBannerAd.cache.hide();
        //设置开启状态
        WxBannerAd.showStatus = false;
    }

    //销毁banner广告
    static destroy(): void {
        //销毁banner广告
        if (WxBannerAd.cache) WxBannerAd.cache.destroy();
        WxBannerAd.cache = null;
        //设置开启状态
        WxBannerAd.showStatus = false;
    }

    //监听banner广告尺寸变化事件
    static onResize(res: any): void {
        WxBannerAd.cache.offResize(WxBannerAd.onResize);
        if (WxBannerAd.styleHasBottom){
            const top = WxBannerAd.windowHeight - res.height - WxBannerAd.styleBottomNum - (WxBannerAd.windowHeight / WxBannerAd.windowWidth > 1.8 ? 15 : 0);
            WxBannerAd.cache.style.top = top + WxBannerAd.deltaY;
        }
        if (WxBannerAd._onResize) {
            WxBannerAd._onResize(res);
            WxBannerAd._onResize = null
        }
    }

    //监听banner广告加载事件
    static onLoad(): void {
        WxBannerAd.loadStatus = true;
        WxBannerAd.cache.offLoad(WxBannerAd.onLoad);
        if (WxBannerAd._onLoad) {
            WxBannerAd._onLoad(WxBannerAd.cache.style);
            WxBannerAd._onLoad = null;
        }
        // WxBannerAd.cache.show();
    }

    //监听banner广告错误事件
    static onError(res: any): void {
        WxBannerAd.cache.offError(WxBannerAd.onError);
        switch (res.errCode) {
            case 1002://广告单元无效
            case 1005://广告组件审核中
            case 1006://广告组件被驳回
            case 1007://广告组件被封禁
            case 1008://广告单元已关闭
                WxBannerAd.adUnitIdStatus[WxBannerAd.curAdUnitId] = false;
                break;
            case 1000://后端接口调用失败
            case 1001://参数错误
            case 1003://内部错误
            case 1004://无合适的广告
            default:
                WxBannerAd.adUnitIdFailNum[WxBannerAd.curAdUnitId]++;
                setTimeout(() => {
                    WxBannerAd.load();
                }, 3000);
                break;
        }
        if (WxBannerAd._onError) {
            WxBannerAd._onError(res);
            WxBannerAd._onError = null;
        }
    }

    //随机获取一个adUnitId
    static randomAdUnitId() {
        if (WxBannerAd.adUnitIds.length) {
            return WxBannerAd.adUnitIds[Math.floor(Math.random() * WxBannerAd.adUnitIds.length)];
        } else {
            return null;
        }
    }

    //移除当前使用的广告id
    static removeCurAdUnitId(): void {
        if (WxBannerAd.adUnitIds.length && WxBannerAd.adUnitIds.indexOf(WxBannerAd.curAdUnitId) !== -1) {
            if (WxBannerAd.adUnitIdsErr.indexOf(WxBannerAd.curAdUnitId) === -1)
                WxBannerAd.adUnitIdsErr.push(WxBannerAd.curAdUnitId);
            WxBannerAd.adUnitIds.splice(WxBannerAd.adUnitIds.indexOf(WxBannerAd.curAdUnitId), 1);
        }
        WxBannerAd.curAdUnitId = null;
    }

    //切换前当的广告id
    static switchCurAdUnitId(adUnitId: string = null) {
        if (adUnitId && WxBannerAd.adUnitIds.indexOf(adUnitId) !== -1) {
            WxBannerAd.curAdUnitId = adUnitId;
        } else {
            WxBannerAd.curAdUnitId = WxBannerAd.randomAdUnitId();
        }
    }

    //游戏场景宽度转设备可使用窗口宽度的转化
    static stage2windowWidth(sw: number) {
        return sw * WxBannerAd.windowWidth / WxBannerAd.stageWidth;
    }

    //游戏场景高度转设备可使用窗口高度的转化
    static stage2windowHeight(sh: number) {
        return sh * WxBannerAd.windowHeight / WxBannerAd.stageHeight;
    }

    //设备可使用窗口宽度转游戏场景宽度的转化
    static window2stageWidth(ww: number) {
        return ww * WxBannerAd.stageWidth / WxBannerAd.windowWidth;
    }

    //设备可使用窗口高度转游戏场景高度的转化
    static window2stageHeight(wh: number) {
        return wh * WxBannerAd.stageHeight / WxBannerAd.windowHeight;
    }
}
```

## 注意事项

文档里面 这样写道

```javascript
const customAd = wx.createCustomAd({
  adUnitId: 'adUnit-xxxx',
  style: {
    left: 10,
    top: 76,
    width: 375, // 用于设置组件宽度，只有部分模板才支持，如矩阵格子模板
    fixed: true // fixed 只适用于小程序环境
  }
})
```

其实这个 很有迷惑性，刚开始以为格子广告是官网说明的那样，是固定的 尺寸 

![image-20221220010825162](CocosCreator%E5%BE%AE%E4%BF%A1%E5%B0%8F%E6%B8%B8%E6%88%8F%E6%8E%A5%E5%85%A5%E5%8E%9F%E7%94%9F%E6%A8%A1%E6%9D%BF-%E6%A0%BC%E5%AD%90%E5%B9%BF%E5%91%8A/image-20221220010825162.png)

![image-20221220010851891](CocosCreator%E5%BE%AE%E4%BF%A1%E5%B0%8F%E6%B8%B8%E6%88%8F%E6%8E%A5%E5%85%A5%E5%8E%9F%E7%94%9F%E6%A8%A1%E6%9D%BF-%E6%A0%BC%E5%AD%90%E5%B9%BF%E5%91%8A/image-20221220010851891.png)

**其实 不然，格子广告是可以设置 width的 而且还有 一个默认最小 宽度 应该是 300 左右。(这些仅仅是个人推断)**

![image-20221220011141957](CocosCreator%E5%BE%AE%E4%BF%A1%E5%B0%8F%E6%B8%B8%E6%88%8F%E6%8E%A5%E5%85%A5%E5%8E%9F%E7%94%9F%E6%A8%A1%E6%9D%BF-%E6%A0%BC%E5%AD%90%E5%B9%BF%E5%91%8A/image-20221220011141957.png)

参加链接：[原生模板广告显示问题](https://developers.weixin.qq.com/community/develop/doc/00008aa06806b037d12ccc7f95b000)

## 其他参考

- [微信广告- 流量主操作指引](https://ad.weixin.qq.com/pdf.html?id=851)
- [微信广告- 小程序原生模板广告](https://ad.weixin.qq.com/pdf.html?post_id=U2FsdGVkX1+ZTD+suHBEHadR+vmX9KTCrIYmLXErm/k=)
- [小游戏原生模板广告找不到其他类型？](https://developers.weixin.qq.com/community/develop/doc/000646eb7607e087fc7d8502b51400)

