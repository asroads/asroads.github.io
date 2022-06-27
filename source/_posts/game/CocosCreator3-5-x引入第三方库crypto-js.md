---
title: CocosCreator3.5.x引入第三方库crypto-js
comments: true
categories: game
tags:
  - Cocos
  - crypto-js
abbrlink: 606dcf1e
date: 2022-06-27 13:38:31
---

今天在Cocos技术群里跟群里交流小游戏开发技巧和经验，有群友遇到了[crypto-js](https://www.npmjs.com/package/crypto-js)这个类库在`CocosCreator`如何使用，记得`2.x`的时候自己测试过，貌似没有成功，于是今天再次拾起来，综合最近所学所悟搞定后记录一下，整个操作过程，便于后面用到的时候查找。
<!--more-->

## 环境

- Cocos Creator 3.5.2
- [crypto-js](https://www.npmjs.com/package/crypto-js)
- Windows系统

## 步骤

### 下载类库编译

1.  先去官网地址下载我们的类库 https://github.com/brix/crypto-js
2. 下载后解压文件，这里使用`Webstorm`打开编译
3. 我们看到里面有个 `grunt`目录 职业习惯感觉是个编译工具 于是找到 `Webstorm`对应的[帮助文档](https://jetbrains.com.zh.xy2401.com/help/webstorm/using-grunt-task-runner.html)

> #### 从Grunt工具窗口构建任务树﻿
>
> - 在“ **grunt”**工具窗口中，单击![添加按钮](CocosCreator3-5-x%E5%BC%95%E5%85%A5%E7%AC%AC%E4%B8%89%E6%96%B9%E5%BA%93crypto-js/icons.general.add@2x.png)在工具栏上，然后从列表中选择所需的`Gruntfile.js`文件。默认情况下，`WebStorm`在项目的根目录中显示`Gruntfile.js`文件。
> - 如果您还有另一个`Gruntfile.js`文件，请单击**“选择`Gruntfile.js`”** ，然后在打开的对话框中选择所需的`Gruntfile.js`文件。`WebStorm`在其标题上添加一个具有所选`Gruntfile.js`文件路径的新节点，并在新节点下构建任务树。

4. 按照步骤3的提示 修改 `grunt\tasks` 目录下的 `modularize.js` 为 `Gruntfile.js` 然后 使用`webstorm` 右键 生成对应的任务树
5. 我们这里选择 `build` 任务 生成我们的库文件 运行任务后 生成一个 build目录 找到我们要的文件 `crypto-js.js`

![image-20220627134813233](CocosCreator3-5-x%E5%BC%95%E5%85%A5%E7%AC%AC%E4%B8%89%E6%96%B9%E5%BA%93crypto-js/image-20220627134813233.png)

6. 去官方网站 下载我们对应是声明文件地址：https://github.com/DefinitelyTyped/DefinitelyTyped/tree/master/types/crypto-js。
7. 下载后 找到 下载后里面的 `index.d.ts` 

### 新建CocosCreator项目

1. 新建一个项目 写入测试代码：

注意  不要第一行的引入 直接 因为我们接下来要添加全局变量

```typescript
var data = [{id: 1}, {id: 2}]

// Encrypt
var ciphertext = CryptoJS.AES.encrypt(JSON.stringify(data), 'secret key 123').toString();

// Decrypt
var bytes  = CryptoJS.AES.decrypt(ciphertext, 'secret key 123');
var decryptedData = JSON.parse(bytes.toString(CryptoJS.enc.Utf8));

console.log(decryptedData); // [{id: 1}, {id: 2}]
```

2. 把刚刚的文件  `crypto-js.js` 复制到 我们的项目内 这里为了方便 新建一个目录叫做 `libs` ，然后把 `index.d.ts`  声明文件 修改为 `crypto-js.d.ts` 

### 修改部分兼容添加全局变量

1. 修改`crypto-js.js` 文件 首先文件里面搜索 关键字 `require` 找到 这里 注释掉

![image-20220627135241004](CocosCreator3-5-x%E5%BC%95%E5%85%A5%E7%AC%AC%E4%B8%89%E6%96%B9%E5%BA%93crypto-js/image-20220627135241004.png)



2. 找到文件最后部分 添加一行代码：`window.CryptoJS =CryptoJS;` 这个 代码 在 `return` 前添加即可 

![image-20220627135349332](CocosCreator3-5-x%E5%BC%95%E5%85%A5%E7%AC%AC%E4%B8%89%E6%96%B9%E5%BA%93crypto-js/image-20220627135349332.png)

最后运行我们的项目 输出结果：

![image-20220627135503288](CocosCreator3-5-x%E5%BC%95%E5%85%A5%E7%AC%AC%E4%B8%89%E6%96%B9%E5%BA%93crypto-js/image-20220627135503288.png)

最后 放上测试源码项目地址 [点击前往](https://github.com/jsroads/mylibs/tree/main/Crydemo)



## 其他用法

Cocos Creator3.x中使用AES加密解密 直接使用 不需要修改 注意需要引入文件 完整路径。

```typescript
import CryptoJS  from "crypto-js.min.js";
const aseKey = "12345678"     //秘钥必须为：8/16/32位
var message = "abcd";
// https://forum.cocos.org/t/topic/106414
//加密
var encrypt = CryptoJS.AES.encrypt(message, CryptoJS.enc.Utf8.parse(aseKey), {
  mode: CryptoJS.mode.ECB,
  padding: CryptoJS.pad.Pkcs7
}).toString();
console.log(encrypt);    //nlW4ll0zjyXE7NvC/wO9rQ==

//解密
var decrypt = CryptoJS.AES.decrypt(encrypt, CryptoJS.enc.Utf8.parse(aseKey), {
  mode: CryptoJS.mode.ECB,
  padding: CryptoJS.pad.Pkcs7
}).toString(CryptoJS.enc.Utf8);
console.log(decrypt);    //abcd
```

作者：翼2021
链接：https://juejin.cn/post/6990264979991560223
来源：稀土掘金

## grunt构建相关

grunt是**JavaScript 世界的构建工具** 有时候我们用第三方库常常可以看到，如果构建项目遇到下面错误，可以这样解决。

1. 报错：

```
'const' is available in ES6 (use 'esversion: 6') or Mozilla JS extensions (use moz).
```

   原因：grunt-contrib-jshint默认是不支持es6 syntax的，如果在被检查的代码中使用es6的新特性，那么就会报错

   解决办法：如何解决：修改`Jshint`配置

```json
 {
 	jshint: {
            options: {
                esversion: 6
            }
 	}
 }
```

2. 报错：

   ```
    src/utils.js
       108 |}
             ^ Missing semicolon.
   ```

   原因: 代码文件缺少分号结尾

   解决办法 在对应的文件处 添加分号 比如上面 就是 `src/utils.js`  108行 缺少分号。

## 参考

- [webstorm-Grunt](https://jetbrains.com.zh.xy2401.com/help/webstorm/grunt-tool-window.html)
- [crypto-js](https://www.npmjs.com/package/crypto-js)
- [Grunt 中文文档](https://www.gruntjs.net/getting-started)

