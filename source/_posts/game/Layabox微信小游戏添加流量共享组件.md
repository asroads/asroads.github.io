---
title: Layabox微信小游戏添加流量共享组件
categories: game
tags:
  - Layabox
abbrlink: 196d2a83
date: 2019-05-08 15:44:34
---

Layabox 官方提供了一个流量共享组件，效果就是让游戏跳转到一个盒子，然后自己的游戏可以在这个用户池子里共流量的分发。这个组件用起来很简单，只能在手机端测试，所以大家在网页版本是看不到内容的。官网的教程已经说的很详细了，但是我还是遇到了一点点问题。

<!-- more -->

### 组件使用步骤

- 首先要去微信公共平台 添加 安全域名 `https://unioncdn.layabox.com`到request合法域名和downloadFile合法域名里。
- 然后在UI编辑器里，你想要展示的UI界面的地方 使用UI编辑器  找到流量共享组件：设计模式 >> 组件 >> Common >> AdvImage ，拖拽AdvImage到游戏UI页面中。
- 如果遇到`TypeScript编写的游戏在最新版本中添加流量共享组件时提示：类型“typeof ui”上不存在属性“AdvImage”`的问题 请在 LayaAir.d.ts 修改 添加下面的代码，即可

```typescript
declare module laya.ui {
    class AdvImage extends Image{
    }
}
```

![image-20190508155224800](Layabox微信小游戏添加流量共享组件/image-20190508155224800.png)

-  做完这些，然后按照自己的逻辑 就可以展示了！

### 参考

- [TypeScript编写的游戏在最新版本...](http://ask.layabox.com/question/39560)
- [流量共享平台上架接入指南](https://share.layabox.com/flow_content.html)