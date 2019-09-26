---
title: Layabox-学习笔记-(7)
categories: game
tags:
  - Layabox
abbrlink: 18269a35
date: 2018-09-03 14:23:10
---

### LayaBox 微信小游戏 游戏分享

我们知道 常见的微信游戏有三种分享 分别是 转发菜单、被动转发、主动转发

<!-- more -->

#### 转发菜单分享

总共分两步：

1. 显示出系统菜单的“转发”按钮
2. 添加系统菜单转发监听函数

##### 显示出系统菜单

用到的函数`wx.showShareMenu(Object object)`

> 支持版本 >= 1.1.0
>
> 显示当前页面的转发按钮
>
> #### 参数
>
> ##### Object object
>
> | 属性            | 类型     | 默认值 | 是否必填 | 说明                                                         | 支持版本 |
> | --------------- | -------- | ------ | -------- | ------------------------------------------------------------ | -------- |
> | withShareTicket | boolean  |        | 是       | 是否使用带 shareTicket 的转发[详情](https://developers.weixin.qq.com/minigame/dev/tutorial/open-ability/share.html) |          |
> | success         | function |        | 否       | 接口调用成功的回调函数                                       |          |
> | fail            | function |        | 否       | 接口调用失败的回调函数                                       |          |
> | complete        | function |        | 否       | 接口调用结束的回调函数（调用成功、失败都会执行）             |          |





> wx.hideShareMenu(Object object)
>
> 支持版本 >= 1.1.0
>
> 隐藏转发按钮
>
> #### 参数
>
> ##### object
>
> | 属性     | 类型     | 默认值 | 是否必填 | 说明                                             | 支持版本 |
> | -------- | -------- | ------ | -------- | ------------------------------------------------ | -------- |
> | success  | function |        | 否       | 接口调用成功的回调函数                           |          |
> | fail     | function |        | 否       | 接口调用失败的回调函数                           |          |
> | complete | function |        | 否       | 接口调用结束的回调函数（调用成功、失败都会执行） |          |



点击右上角按钮，会弹出菜单，菜单中的“转发”选项默认不展示。通过 [wx.showShareMenu()](https://developers.weixin.qq.com/minigame/dev/api/share/wx.showShareMenu.html) 和 [wx.hideShareMenu()](https://developers.weixin.qq.com/minigame/dev/api/share/wx.hideShareMenu.html) 可动态显示、隐藏这个选项。

比如我的实现是：

```typescript
 let shareObj = {
        complete: (scope) => {
            console.log("Complete");
        },
        success: (scope) => {
            console.log("Success");
        },
        fail: (scope) => {
            console.log("Fail");
        }
    };
    wx.showShareMenu(shareObj);
```

##### 添加系统菜单转发监听

需要用到的函数：wx.onShareAppMessage(function callback)  **注意参数是方法**

> #### callback 回调函数
>
> ##### 返回值
>
> ###### Object
>
> | 属性     | 类型   | 说明                                                         | 支持版本 |
> | -------- | ------ | ------------------------------------------------------------ | -------- |
> | title    | string | 转发标题，不传则默认使用当前小游戏的昵称。                   |          |
> | imageUrl | string | 转发显示图片的链接，可以是网络图片路径或本地图片文件路径或相对代码包根目录的图片文件路径。显示图片长宽比是 5:4 |          |
> | query    | string | 查询字符串，必须是 key1=val1&key2=val2 的格式。从这条转发消息进入后，可通过 wx.getLaunchOptionSync() 或 wx.onShow() 获取启动参数中的 query。 |          |

比如我的实现是：

```typescript
    //使用微信菜单分享 path 是你的网络URL地址 或者本地创建
    wx.onShareAppMessage(() => {
        return {
            title: '微信小游戏分享标题',
            imageUrl: "path",
            success: (scope) => {
                console.log("ShareAppMessageSuccess");
            },
            fail: (scope) => {
                console.log("ShareAppMessageFail");
            }
        }
    });
```

#### 主动分享

`wx.shareAppMessage(Object object)`

> #### 参数
>
> ##### Object object
>
> | 属性     | 类型   | 默认值 | 是否必填 | 说明                                                         | 支持版本 |
> | -------- | ------ | ------ | -------- | ------------------------------------------------------------ | -------- |
> | title    | string |        | 否       | 转发标题，不传则默认使用当前小游戏的昵称。                   |          |
> | imageUrl | string |        | 否       | 转发显示图片的链接，可以是网络图片路径或本地图片文件路径或相对代码包根目录的图片文件路径。显示图片长宽比是 5:4 |          |
> | query    | string |        | 否       | 查询字符串，从这条转发消息进入后，可通过 wx.getLaunchInfoSync() 或 wx.onShow() 获取启动参数中的 query。必须是 key1=val1&key2=val2 的格式。 |          |

比如我的实现：

```typescript
let obj = {
    title: "分享的标题",
    imageUrl: "path",
    success: (scope) => {
       console.log("ShareSuccess");
    }
 };
 wx.shareAppMessage(obj);
```

可能要了解的还有有 `wx.getShareInfo(Object object)`、`wx.hideShareMenu(Object object)`、`wx.offShareAppMessage(function callback)`、`wx.updateShareMenu(Object object)`

参考文档：

- [微信小游戏API](https://developers.weixin.qq.com/minigame/dev/document/render/canvas/wx.createCanvas.html) （比较权威，更新和接口更改都有）
- [w3cschool微信小游戏API](https://www.w3cschool.cn/wxagame/wxagame-yt462itn.html)（可以搜索）