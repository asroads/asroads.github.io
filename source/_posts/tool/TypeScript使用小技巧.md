---
title: TypeScript使用小技巧
date: 2019-09-10 19:51:11
categories: "tool"
tags:
- ts
---

在学习过程中使用TypeScript还是遇到了很多巧妙的解决办法，下面整理一下近期遇到的很多问题，和最后使用的解决方案。有些技巧还是很实用的，节省了很多时间和精力。

<!-- more -->

### 如何在window 下面新添加（声明）一个新属性

我们知道TypeScript是强类型的，随便声明一个变量在全局对象window下就好编辑器报错，尝试若干次失败后，在网上找到了解决方法：

英文问题 [How do you explicitly set a new property on `window` in TypeScript?](https://stackoverflow.com/questions/12709074/how-do-you-explicitly-set-a-new-property-on-window-in-typescript)

```typescript
declare global {
    interface Window { MyNamespace: any; }
}
window.MyNamespace = window.MyNamespace || {};
```

