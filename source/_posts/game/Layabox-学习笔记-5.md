---
title: Layabox-学习笔记-(5)
date: 2018-08-24 20:45:33
categories: "game"
tags:
- Layabox
---

### Layabox 关于微信小游戏 用户授权 接入

#### 接入原因

[2018年4月15日微信团队发了新的接口调整的公告](https://developers.weixin.qq.com/community/develop/doc/0000a26e1aca6012e896a517556c01)  

**小程序与小游戏获取用户信息接口调整，请开发者注意升级**

<!-- more -->

> 为优化用户体验，使用 wx.getUserInfo 接口直接弹出授权框的开发方式将逐步不再支持。从2018年4月30日开始，小程序与小游戏的体验版、开发版调用 wx.getUserInfo 接口，将无法弹出授权询问框，默认调用失败。正式版暂不受影响。开发者可使用以下方式获取或展示用户信息：
>
>
>
> 一、小程序:
>
> 1、使用 button 组件，并将 open-type 指定为 getUserInfo 类型，获取用户基本信息。
>
> 详情参考文档:
>
> <https://developers.weixin.qq.com/miniprogram/dev/component/button.html>
>
>
>
> 2、使用 open-data 展示用户基本信息。
>
> 详情参考文档:
>
> <https://developers.weixin.qq.com/miniprogram/dev/component/open-data.html>
>
>
>
> 二、小游戏：
>
> 1、使用用户信息按钮 UserInfoButton。
>
> 详情参考文档:
>
> <https://developers.weixin.qq.com/minigame/dev/document/open-api/user-info/wx.createUserInfoButton.html>
>
>
>
> 2、开放数据域下的展示用户信息。
>
> 详细参考文档：
>
> <https://developers.weixin.qq.com/minigame/dev/document/open-api/data/wx.getUserInfo.html>
>
>
>
> 请各位开发者注意及时调整接口。

这个时候 我们知道 第三条 是关于小游戏 用户信息的，以前我们登录的生活 直接获取用户信息，如果没有授权的用户自动弹出用户授权信息，已经授权的则不会弹出，这次修改改变了原来的逻辑。

#### 更新办法

在微信小游戏API版本>=2.0.1之后 就可以使用新的方法 `UserInfoButton wx.createUserInfoButton(Object object)` 来实现以前的功能。

以前我们可以这样写：

```javascript
private starGame() {
    const userInfo = game platform.getUserInfo();//game 是你游戏对象的实例
    console.log("用户数据：",userInfo);
}
```

以后要改用新的API规则自己主动调用。

官方给出的例子:

```javascript
let button = wx.createUserInfoButton({
    type: 'text',
    text: '获取用户信息',
    style: {
        left: 10,
        top: 76,
        width: 200,
        height: 40,
        lineHeight: 40,
        backgroundColor: '#ff0000',
        color: '#ffffff',
        textAlign: 'center',
        fontSize: 16,
        borderRadius: 4
    }
})
button.onTap((res) = > {
    console.log(res)
})
```

表示可以用两种类型  文本按钮 和图片按钮 来触发  下面的 style 对象是设置 按钮的样式 还有一些其他的参数是可选的。大家可以自行去看官方的API 写 的很详细。《[UserInfoButton wx.createUserInfoButton(Object object)](https://developers.weixin.qq.com/minigame/dev/document/open-api/user-info/wx.createUserInfoButton.html)》

这样 我们在登录游戏的时候  可以通过 用户的设置信息 获得 有没有经过授权，然后判断是否主动弹出让用户授权的页面。接口是 `wx.getSetting(Object object)`[API地址点击查看](https://developers.weixin.qq.com/minigame/dev/document/open-api/setting/wx.getSetting.html)

返回的信息下面 有个 `authSetting`的属性 里面包含一个`scope.userInfo`的属性。这个属性是个boolean类型的，我们可以根据这个类型进行操作。

**问题得以解决**

我的示例代码如下：

```typescript
/**
 * Created by asroads on 2018/8/21 上午11:45
 * Note:  微信授权UI
 */
module smile {
    export class MyAuthorize {
        /**
         * 获取用户设置信息（关于授权...）
         */
        public static getWXSetting = () => {
            let seting = {
                complete: (scope) => {
                    console.log("wxsetingComplete");
                },
                success: (scope) => {
                    // console.log("setingSuccess:" + JSON.stringify(scope));
                    if (scope && scope.authSetting && scope.authSetting['scope.userInfo']) 
                        MyAuthorize.showCallbackFunction();//如果已经授权
                    } else {
                        console.log("notAuthorize");
                        MyAuthorize.initUserInfoButton();
                    }
                },
                fail: (scope) => {
                    console.log("wxsetingFail");
                }
            };
            mygame.platform.Instance.getSetting(seting);
        };
        /**
         *  显示开始游戏
         */
        private static showCallbackFunction = () => {
          console.log("这里写你的开始游戏界面");
        };
        /**
         * 初始化 用户信息授权按钮 和监听点击事件
         */
        private static initUserInfoButton = () => {
            let button = MyAuthorize.creatUserInfoButton();
            button.onTap((res) => {
                // console.log("sucess:" + JSON.stringify(res));
                button.destroy();
            });
            button.show();
        };
        /**
         * 创建 按钮
         */
        private static creatUserInfoButton = () => {
            let imgWidth: number = 279;
            let imgHeight: number = 179;
            let button = mygame.platform.Instance.createUserInfoButton({
                type: 'image',
                text: '获取用户信息',
                image: 'path/login.png',
                style: {
                    left: (Laya.Browser.clientWidth - imgWidth) / 2,
                    top: (Laya.Browser.clientHeight - imgHeight) / 2,
                    width: imgWidth,
                    height: imgHeight,
                    lineHeight: 40,
                    backgroundColor: '#ff0000',
                    color: '#ffffff',
                    textAlign: 'center',
                    fontSize: 16,
                    borderRadius: 4
                },
                withCredentials: true,
                lang: 'zh_CN'
            });
            return button;
        }
    }
}


```



### Layabox 关于微信小游戏 网络加载



刚开始我按照官方文档 设置了白名单，但是本地测试一直是采用的网络加载，后来才发现，这个设置**只有在微信平台下才起作用**，所以我在 chome 和 layaair本地测试无效。是layabox 专为微信API 特意定制的设置方法



> ```typescript
> material.diffuseTexture = Laya.Texture2D.load("res/layabox.png");
> box.meshRender.material = material;
> Laya.URL.basePath = "https://XXXX.com";//请把XXXX换成自己的真实网址；
> //在此之下，再使用load加载资源，都会自动加入URL网址。从网络上动态加载。
> ```



白名单 设置也很简单

> ```typescript
> MiniAdpter.nativefiles =  [
>     "wxlocal",
>     "res/atlas/houzi.atlas",
>     "res/atlas/houzi.png",
>     "common/tishi.png",
>     "common/bg.png",
>     "ui.json",
>     "newLb/bg031.png"
> ];
> 
> ```

Layabox 官方文档 http://ldc.layabox.com/doc/?nav=zh-ts-5-0-4

