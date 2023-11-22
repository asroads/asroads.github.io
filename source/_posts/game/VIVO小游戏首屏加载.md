---
title: VIVO小游戏首屏加载
comments: true
categories: game
tags:
  - 小游戏
  - vivo
abbrlink: 3dab5a00
date: 2023-11-13 20:51:00
---

这里是你博客列表显示的摘要文字最近项目要发布VIVO小游戏，由于游戏是从App转到小游戏的，在微信和抖音的平台表现已经是要做很多优化和品质牺牲，到了VIVO小游戏效果更是不乐观，于是按照官方的优化指导接入了他们的[小游戏自定义Loading组件](https://minigame.vivo.com.cn/documents/#/api/system/loadingView?id=小游戏自定义loading组件)。
<!--more-->
![q9qou4sp](./VIVO小游戏首屏加载/q9qou4sp.png)

## 步骤

首先我们先看官方文档的说明：

> ### [qg.createCustomizeLoading(Object object)](https://minigame.vivo.com.cn/documents/#/api/system/loadingView?id=qgcreatecustomizeloadingobject-object)
>
> #### [1、参数](https://minigame.vivo.com.cn/documents/#/api/system/loadingView?id=_1、参数)
>
> | 属性               | 类型   | 必填 | 说明                                                         |
> | :----------------- | :----- | :--- | :----------------------------------------------------------- |
> | background         | string | 否   | 背景图片，可以为网络图片或本地图片                           |
> | text               | string | 否   | 加载进度文本                                                 |
> | textColor          | string | 否   | 文本的颜色。必须是 16 进制格式的颜色字符串，例如：‘#999999’。 |
> | loadingColorTop    | string | 否   | loading进度条渐变色顶部颜色。必须是 16 进制格式的颜色字符串，默认无背景色，例如：‘#999999’。 |
> | loadingColorBottom | string | 否   | loading进度条渐变色底部颜色。必须是 16 进制格式的颜色字符串，默认无背景色，例如：‘#999999’。 |
> | loadingProgress    | Number | 否   | 进度条的进度                                                 |
> | flow               | Number | 否   | 首屏所需资源大小，显示流量消耗提示，单位Mb。若首屏所需资源超过30Mb，则必须提供该值，否则可能审核不通过 |
>
> **返回值**
>
> 创建CustomizeLoading组件
>
> ### [CustomizeLoading.update(Object object)](https://minigame.vivo.com.cn/documents/#/api/system/loadingView?id=customizeloadingupdateobject-object)
>
> 更新CustomizeLoading样式，传入参数与createCustomizeLoading一致
>
> ### [CustomizeLoading.getProgress(Object object)](https://minigame.vivo.com.cn/documents/#/api/system/loadingView?id=customizeloadinggetprogressobject-object)
>
> 获取CustomizeLoading组件的进度
>
> #### [参数](https://minigame.vivo.com.cn/documents/#/api/system/loadingView?id=参数)
>
> Object object
>
> | 属性    | 类型     | 必填 | 说明     |
> | :------ | :------- | :--- | :------- |
> | success | Function | 否   | 成功回调 |
> | fail    | Function | 否   | 失败回调 |
>
> ##### [success回调参数：Object data](https://minigame.vivo.com.cn/documents/#/api/system/loadingView?id=success回调参数：object-data)
>
> | 属性            | 类型   | 说明            |
> | :-------------- | :----- | :-------------- |
> | currentProgress | Number | 当前loading进度 |
>
> ### [CustomizeLoading.remove()](https://minigame.vivo.com.cn/documents/#/api/system/loadingView?id=customizeloadingremove)
>
> 销毁CustomizeLoading组件
>
> ### [CustomizeLoading.resetLoading() `1092+`](https://minigame.vivo.com.cn/documents/#/api/system/loadingView?id=customizeloadingresetloading-1092)
>
> 重置CustomizeLoading组件进度

我们可以看出，其实需要开发者 给出一张图片，本地相对路径或者网络远程地址作为显示，可以设置进度条的颜色，然后通过自己调用`update` 更新进度，当更新完成的时候，或者适当的时机，可以调用 `remove`函数，移除自定义加载组件。

下面给出我写的一个用例代码脚本名字: `vivo-custom-loading.js`

```javascript

window.boot();
//使用该功能时，需判断引擎是否支持
//创建CustomizeLoading组件
if (qg.createCustomizeLoading) {
    console.log("VIVO 添加 createCustomizeLoading");
    // 生成随机数和时间戳
    const randomNumber = Math.floor(Math.random() * 100000) + 1;
    const timestamp = Date.now();
    let url = "https://xxxx/xxxx/cover4.jpg";
    // let url = "screen/cover5.jpg";
    url += '?';
    url += 'v=' + randomNumber;
    url += '&t=' + timestamp;
    console.log("url------", url)
    window['VIVOCustomizeLoading'] = qg.createCustomizeLoading({
        background: '/cover.jpg',
        text: '正在加载中...',
        textColor: '#ffffff',
        loadingColorTop: '#ffffff',
        loadingColorBottom: '#000000',
        loadingProgress: 0
    });
    //获取CustomizeLoading进度
    //根据实际场景进行更新进度（只能增加）、背景、文字以及文字颜色
    //更新CustomizeLoading样式
    window['VIVOCustomizeLoadingCount'] = 1;
    window['VIVOCustomizeLoadingRate'] = 1;
    const CustomizeLoadingId = setInterval(() => {
        if (window['VIVOCustomizeLoading'] === null || window['VIVOCustomizeLoading'] === undefined) {
            console.log("已经提前完成任务");
            clearInterval(CustomizeLoadingId);
        } else {
            window['VIVOCustomizeLoadingCount'] += (1*window['VIVOCustomizeLoadingRate']);
            window['VIVOCustomizeLoadingCount'] = Math.min(100,window['VIVOCustomizeLoadingCount']);
            const progress = Math.floor(window['VIVOCustomizeLoadingCount']);
            // console.log("VIVOCustomizeLoadingCount------", window['VIVOCustomizeLoadingRate']);
            if( window['VIVOCustomizeLoadingRate']>1){
                window['VIVOCustomizeLoadingRate']*=1.25;
            }
            window['VIVOCustomizeLoading'].update({
                background: '/cover.jpg',
                text: '正在加载中...',
                textColor: '#ffffff',
                loadingColorTop: '#ffffff',
                loadingColorBottom: '#000000',
                loadingProgress: progress
            })
            window['VIVOCustomizeLoading'].getProgress({
                success: (data) => {
                    console.log(`handling success: ${data.currentProgress}`)
                    if (data.currentProgress >= 100) {
                        console.log("VIVO模拟加载完成");
                        clearInterval(CustomizeLoadingId);
                        window['VIVOCustomizeLoading'] && window['VIVOCustomizeLoading'].remove();
                        window['VIVOCustomizeLoading'] = null;
                    }
                },
                fail: (data, code) => {
                    console.log(`handling fail, code = ${code}`)
                }
            })
        }
    }, 75)
} else {
    console.log("系统不支持")
}
```

这里值得注意的是 `window['VIVOCustomizeLoadingRate']` 这个属性，游戏在正常打开第一个场景后 我会在游戏内调用：

```javascript
  if(如果是vivo小游戏){
            if (window['VIVOCustomizeLoading']) {
                console.log("游戏内提前显示要移除自定义loading");
                window['VIVOCustomizeLoadingRate'] = 1.25;
                // window['VIVOCustomizeLoading'] && window['VIVOCustomizeLoading'].remove();
                // window['VIVOCustomizeLoading'] = null;
            }
        }
```

## 拓展

如果 进入游戏 马上调用 
![2f23406b359b414109555f5c6273da79](./VIVO小游戏首屏加载/2f23406b359b414109555f5c6273da79.png)

游戏会报错 ：
![image-20231113212221810](./VIVO小游戏首屏加载/image-20231113212221810.png)

原因是：创建之后就进行获取导致的，可以延迟调用即可。