---
title: Cocos Creator 采坑日记，看看你有没有遇到过
comments: true
categories: game
tags:
  - Cocos
abbrlink: bdf287b3
date: 2019-12-25 22:01:18
---

在开发游戏的过程中，多多少少都会采坑，有时候是自己对API理解的不够透彻，引起的误解，有时候是问题报错查找起来很费功夫，总的来讲，很多问题值得记录和分析参考，故而记下来，那么看看你再开发的过程中有没有遇到这样的坑呢？
<!--more-->
本文主要说Cocos Creator 引擎在开发游戏中遇到的点点滴滴，后面会不定时更新，及时添加遇到的新的问题。

### 网页不报错，小游戏模拟器报错

```bash
Uncaught ReferenceError: loadBabelMod is not defined
```

![image-20191226092601253](Cocos-Creator-采坑日记，看看你有没有遇到过/image-20191226092601253.png)

**原因：**游戏内我使用了ES6开发，编辑器内我勾选了 增强编译：

关于增强编译可以参考这个地址：[微信开发者工具 1.02.1905081 更新说明](https://developers.weixin.qq.com/community/develop/doc/00066877c54eb0ff5488b54885b801)

> 新版工具增加了`增强编译`的选项来增强`ES6转ES5`的能力，启用后会使用新的编译逻辑以及提供额外的选项供开发者使用。
>
> 启用`增强编译`后的编译能力的对比：
>
> | 特性         |    原有逻辑    |        增强编译        |
> | :----------- | :------------: | :--------------------: |
> | Babel版本    |     babel6     |         babel7         |
> | Presets      | es2015、stage0 | env {chrome:53, ios:8} |
> | Helpers      |   单文件内联   |       跨文件共享       |
> | Async/Await  |     不支持     |          支持          |
> | 严格模式开关 |     不支持     |          支持          |
> | 忽略文件目录 |     不支持     |          支持          |
> | 代码压缩     |   uglify-js    |         terser         |
> | Babel插件    |       -        |   一系列proposal`*`    |
> | polyfill     |   大部分es6    |  新增三个polyfill`*`   |
>
> - 使用`preset-env`,支持最新的`ECMAScript`语法
> - 共享helpers函数，默认放在项目`@babel/runtime`目录，可通过`项目配置文件`配置
> - 支持async/await语法，按需注入`regeneratorRuntime`，目录位置与helpers函数一致
> - 文件首行是`// use strict disable;`时，即可禁用文件严格模式
> - 可通过`项目配置文件`指定任意文件、目录不经过编译（如：miniprogram_npm）
> - 原有逻辑是支持`stage0`语法的，为了向前兼容，[引入了一系列proposal插件](https://github.com/babel/babel/blob/master/packages/babel-preset-stage-0/README.md)
> - 关于polyfill，基础库中已经引入了大量的`es6`相关的polyfill [可参考文档](https://developers.weixin.qq.com/miniprogram/dev/guide/runtime/js-support.html)，增强编译下，新增：Array.prototype.includes`(es7)`、Object.entries`(es8)`、Object.values`(es8)`
>
> ##### 开启增强编译
>
> 项目详情页中，勾选`增强编译`选项
> ![img](Cocos-Creator-采坑日记，看看你有没有遇到过/0.jpeg)
>
> 打开后，即可在项目中使用最新的js语法
>
> ![img](Cocos-Creator-采坑日记，看看你有没有遇到过/0-20191226091649636.jpeg)
>
> 工具运行时，会按需注入一些辅助函数和regeneratorRuntime， 注入的目录路径可通过配置修改，上传代码时（包括预览时）这些文件会作为代码包的一部分
>
> ![img](Cocos-Creator-采坑日记，看看你有没有遇到过/0-20191226091654928.jpeg)
>
> ##### 其他选项
>
> 其他可选项详情请参考[项目配置文档](https://developers.weixin.qq.com/miniprogram/dev/devtools/projectconfig.html)

其实 看了上面的说明和解释，问题基本明了，就是开放域也要使用ES6 才行，解决问题大概有两个方向

- 游戏内不用ES6语法，取消勾选 增强编译
- 继续使用，语法格式保持统一

作为追求进步的编码者，肯定是想尽办法也是用第二种策略，于是我找到我开放域的代码，重新写了一遍，代码成功运行，此处，也遇到一个问题是

`this is undefined` 这个错误，之前一直都是正常的，怎么突然就报了这个错呢，后面我再阅读文档遇到了一段解释，才算是 明白了。地址（[点击前往](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Functions/Arrow_functions)）

> 在箭头函数出现之前，每一个新函数根据它是被如何调用的来定义这个函数的this值：
>
> - 如果是该函数是一个构造函数，this指针指向一个新的对象
> - 在严格模式下的函数调用下，this指向undefined
> - 如果是该函数是一个对象的方法，则它的this指针指向这个对象
> - 等等
>
> `This`被证明是令人厌烦的面向对象风格的编程。

### Button 的 interactable 属性设置 不生效

编辑设置 这个属性，是正常的，但是在代码里 我动态调用后，输出值是正确的，但是UI没有生效

![image-20191226093032830](Cocos-Creator-采坑日记，看看你有没有遇到过/image-20191226093032830.png)

我更改后输出打印的值：

![image-20191226093106999](Cocos-Creator-采坑日记，看看你有没有遇到过/image-20191226093106999.png)

怪哉，怪哉！很奇怪，但是我再编辑器里 如果一开始设置了 就是正确的，其实后面通过我和之前正常的仔仔细细对比之后，得出结论是：

**原因：**我动态调用的代码运行在 这个脚本组件的 `onLoad` 函数之前，此时虽然我代码调用了这个属性，但是我的组件的`onLoad`函数还没有执行，我把调用的代码 放到 `onLoad` 函数调用 后 就可以正常显示！！这个是对组件的渲染和运行的模型了解不够透彻，才入坑，基本遇到后续这样的调用，都应该改成，提前调用只更改数据，等onLoad调用的时候，再去用数据 驱动UI的改变。

### 模拟器报错 Cannot read property '0' of undefined

```
Uncaught TypeError: Cannot read property '0' of undefined
```

其实从错误 我们 看不太明白是什么问题 

![image-20191226093700223](Cocos-Creator-采坑日记，看看你有没有遇到过/image-20191226093700223.png)

我们点击错误 看看报错的代码的地方

![image-20191226093910764](Cocos-Creator-采坑日记，看看你有没有遇到过/image-20191226093910764.png)

**原因：**既然直接指向了 这个组件脚本，那么肯定是这个脚本有问题，后面经过查找得知是 import的时候，文件的路径写的不正确，平时我自己写 引入 都是用 编辑器自动引入的，这个应该是别人从别的地方 复制，粘贴，导致的错误

解决办法：既然是引入错误，修复引入路径即可

```javascript
import GameData from "../../GameData";
```

修改为：

```javascript
import GameData from "../GameData";
```

其实可以看出，如果 引入路径不正确，都会引发这个错误，所以我们再引入文件对象的时候，对文件路径要仔细校对。善用工具（快捷键 or 自动引入都是比较好的办法）。



### 总结

在开发过程中，遇到问题，不要逃避。这样问题会积少成多，后面不利于自己的技术的进步和成长，知其然，知其所以然，才是靠近真相的最佳途径。成长是一个渐进式的厚积薄发的过程，只有点点滴滴的努力，去查找，去分析，去思考问题的本质。