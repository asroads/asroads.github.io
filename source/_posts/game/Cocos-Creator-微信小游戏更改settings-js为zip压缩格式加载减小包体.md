---
title: Cocos Creator 微信小游戏更改settings.js为zip压缩格式加载减小包体
comments: true
abbrlink: 16d8396f
date: 2022-10-14 10:02:46
categories:
tags:
---

新版本CocosCreator已经对小游戏平台做了不少优化，其中包括更快速的渲染首屏页面，把资源配置文件格式修改为json格式（相当于可以存放到远程资源服务器）从而给代码留出更多的空间（首包4MB，总包不超过20MB），对于很多游戏来说是件很好的事情，但对于一直使用旧版本引擎开发的人来讲，需要自己做这些事情，首屏的问题社区已经给了很好的方案，这里不再赘述，文章后面会给出几个优秀的链接，大家自己根据文章里的步骤做就好了，下面说说如何把资源settings.js修改为json，然后压缩为zip，加载到游戏内使用。
<!--more-->

由于操作的项目早期资源没有资源设计，全部一股脑放到了resources里面，导致出包的时候settings文件很大，和代码相当。于是就想着要么让文件瘦身，要么压缩。瘦身是件长期有益但是耗时比较久容易引发问题，需要精力和时间，鉴于工期时间紧，先做压缩，后期再做资源调整优化处理。

## 环境

- Cocos Creator 2.3.3
- 系统环境 Windows 10
- 用到的技术 `nodejs`

## 步骤

1. 首先我们把我们项目构建为微信小游戏，然后找到构建后的settings.js

![image-20220815101534420](Cocos-Creator-%E5%BE%AE%E4%BF%A1%E5%B0%8F%E6%B8%B8%E6%88%8F%E6%9B%B4%E6%94%B9settings-js%E4%B8%BAzip%E5%8E%8B%E7%BC%A9%E6%A0%BC%E5%BC%8F%E5%8A%A0%E8%BD%BD%E5%87%8F%E5%B0%8F%E5%8C%85%E4%BD%93/image-20220815101534420.png)

2. 然后查看一下 小游戏如何引用的（这里简单的讲述一下加载引用逻辑，这样我们可以做到知其然，知其所以然），首先我们打开 构建后的代码`game.js` 文件，然后查看源码。

![image-20220815102101754](Cocos-Creator-%E5%BE%AE%E4%BF%A1%E5%B0%8F%E6%B8%B8%E6%88%8F%E6%9B%B4%E6%94%B9settings-js%E4%B8%BAzip%E5%8E%8B%E7%BC%A9%E6%A0%BC%E5%BC%8F%E5%8A%A0%E8%BD%BD%E5%87%8F%E5%B0%8F%E5%8C%85%E4%BD%93/image-20220815102101754.png)

```javascript
require('./src/settings');
```

3. 这个就是代码的引入逻辑。然后查看一下 `settings.js`文件内容

```
window._CCSettings={
platform:"wechatgame",groupList:[]
...//此处省略源文件内容 基本都是资源的信息
};(function(e){var t=e.uuids,s=e.md5AssetsMap;for(var i in s)for(var r=s[i],n=0;n<r.length;n+=2)"number"==typeof r[n]&&(r[n]=t[r[n]])})(window._CCSettings);
```

4. 根据上面的结构，可以看出文件的内容被赋值给了全局变量下的一个属性`_CCSettings`，然后进行了一些操作。
5. 然后我们看另外一个文件`main.js`文件内容。

```javascript
"use strict";

window.boot = function () {
  var settings = window._CCSettings;
  window._CCSettings = undefined;
};
```

我们看到，文件开头就进行了一个再次的引用，然后把全局变量赋值给一个局部变量后，就被置空了，相当于`window._CCSettings`的生命价值和周期就此结束了，于是我们就有了思路，就是在这个过程中我们要先加载对应的`json`文件，然后对内容处理，然后把结果赋值给这个 局部变量 `settings`即可.

6. 如果尽可能的保留原来的代码流程，这里我想到了异步加载后处理，于是我们新建一个js 文件这里叫做 application.js 然后写入以下代码:

```javascript
function loadSettingsJson() {
  const settings = 'engine/src/settings.json';//这里是自己的settings.json路径
  return new Promise(function (resolve, reject) {
    if (typeof fsUtils !== 'undefined' && !settings.startsWith('http')) {
      const result = fsUtils.readJsonSync(settings);
      if (result instanceof Error) {
        reject(result);
      } else {
        window._CCSettings = result;
        console.log("window._CCSettings",window._CCSettings);
        resolve();
      }
    } else {
      const requestSettings = function requestSettings() {
        const xhr = new XMLHttpRequest();
        xhr.open('GET', settings);
        xhr.responseType = 'text';

        xhr.onload = function () {
          window._CCSettings = JSON.parse(xhr.response);
          console.log("window._CCSettings",window._CCSettings);
          resolve();
        };

        xhr.onerror = function () {
          if (retryCount-- > 0) {
            setTimeout(requestSettings, retryInterval);
          } else {
            reject(new Error('request settings failed!'));
          }
        };
        xhr.send(null);
      };
      let retryCount = 3;
      const retryInterval = 2000;
      requestSettings();
    }
  });
}


function loadSettingsZip() {
  const filePath = 'engine/src/settings.zip'; // 下载路径
  const fileManager = wx.getFileSystemManager();
  return new Promise(function (resolve, reject) {
    fileManager.unzip({
      zipFilePath: filePath,   // 资源下载后路径
      targetPath: wx.env.USER_DATA_PATH, // 解压资源存放路径
      success: function (res) {// 解压成功
        console.log("res" , res)
        let result = fileManager.readFileSync(`${wx.env.USER_DATA_PATH}/settings.json`,'utf8')
        window._CCSettings = JSON.parse(result);
        console.log("window._CCSettings",window._CCSettings);
        resolve();
      },
      fail: function (error) {// 解压失败
        console.log("error" , error)
      },

      complete(){
        console.log("complete settings")
      }
    })
  });
}



function composeSettings(e) {
  var t = e.uuids, s = e.md5AssetsMap;
  return new Promise(function (resolve, reject) {
    for (var i in s) {
      for (var r = s[i], n = 0; n < r.length; n += 2) {
        "number" == typeof r[n] && (r[n] = t[r[n]])
      }
    }
    resolve();
  })
}
exports.loadSettingsJson = loadSettingsJson;
exports.loadSettingsZip = loadSettingsZip;
exports.composeSettings = composeSettings;
```

上面给出了三个方法，第一个是 如果不压缩，直接修改格式用，第二个是把 `json`压缩后直接加载`zip`用，第三个其实就是把 原来的`settings.js`代码后面的转换方法复制过来，等待前面加载处理后，再次对数据处理。

7. 做完这些然后回到我们前面提到的 `main.js`文件 修改：

原来内容

```javascript
"use strict";

window.boot = function () {
  var settings = window._CCSettings;
  window._CCSettings = undefined;
```

修改后:

```javascript
"use strict";
 
 const {loadSettingsJson,composeSettings,loadSettingsZip} = require("../fastScreen/application");

window.boot = async function () { 
  await loadSettingsZip(); 
  await composeSettings(window._CCSettings); 

  var settings = window._CCSettings;
  window._CCSettings = undefined;

```

8. 然后去修改我们的资源，把 `settings.js` 里面的 `window._CCSettings` 后面的内容转换为`json`格式然后压缩为zip即可。这里给出一个在线转换`js`对象为`json`的网站（[JavaScript对象转JSON点击前往](http://www.atoolbox.net/Tool.php?Id=1006)）
9. 至此，操作全部结束，可以重新预览效果。

## 脚本一键处理

如果按照上面的步骤每次操作，或者是把一些操作做成模板，构建后操作也可以，但是还是避免不了手动操作，比如修改js 文件为json 然后压缩，对于版本迭代不频繁的，一切好说，但是对于频繁发布版本，或者使用工具构建发布的 就相对来说比较麻烦，于是下面就说一些如何使用脚本操作。

其实主要的步骤是三个：

1. 修改js 内容为 json
2. 对修改后的json 压缩为zip，并且删除json 和js
3. 修改引入对应逻辑

这里我们借助nodejs 来实现这个功能。完整源码（点击前往），下面主要说一些核心逻辑的代码：

#### JavaScript对象转json

```javascript
'use strict';
/**
 * @param {string} s$jscomp$3
 * @return {undefined}
 */
function runit(s$jscomp$3) {
  if (typeof s$jscomp$3 === "undefined") {
    s$jscomp$3 = document.getElementById("txt1").value;
  }
  if (document.getElementById("spanError")) {
    /** @type {string} */
    document.getElementById("spanError").innerText = "";
  }
  if (s$jscomp$3.trim() != "") {
    try {
      if (s$jscomp$3.trim().endsWith("...")) {
        s$jscomp$3 = s$jscomp$3.trim().slice(0, -3);
      }
      /** @type {*} */
      var o$jscomp$0 = eval("(" + s$jscomp$3 + ")");
      var terse$jscomp$0 = document.getElementById("chkTerse").checked;
      /** @type {string} */
      document.getElementById("txta").value = JSON.stringify(o$jscomp$0, null, terse$jscomp$0 ? 0 : 3);
    } catch (e) {
      if (document.getElementById("spanError")) {
        /** @type {string} */
        document.getElementById("spanError").innerText = "Invalid Javascript Object entered.";
      } else {
        alert("Invalid Javascript Object Entered entered.");
      }
    }
  }
}
```

参考: http://www.atoolbox.net/Tool.php?Id=1006

#### 压缩 json 为 zip 格式

这里使用一个第三方库 [jszip](https://github.com/Stuk/jszip)

```javascript
var fs = require('fs');
var path = require('path');
var JSZip = require('jszip');
var config = {    // 文件根目录
    dir:"C:/objs/ariport/"
}

/**
 * 把mtl文件和obj文件打包成zip压缩包
 * @param  {} fileName 不带文件后缀的文件名
 * @param  {} {delSource = false } = {} 是否删除源文件
 */
function toZipOfMtlObj (fileName, { delSource = false } = {}) {
    var zip = new JSZip();
    var extArr = ['.mtl', '.obj'];

    extArr.forEach(ext => {
        let file = fileName + ext;
        let content = getFileContent(fileName + ext);
        zip.file(file, content);
    })

    // 压缩
    zip.generateAsync({
        // 压缩类型选择nodebuffer，在回调函数中会返回zip压缩包的Buffer的值，再利用fs保存至本地
        type: "nodebuffer",
        // 压缩算法
        compression: "DEFLATE",
        compressionOptions: {
            level: 9
        }
    }).then(function (content) {
        let zip = fileName + '.zip';
        // 写入磁盘
        fs.writeFile(getFullFileName(zip), content, function (err) {
            if (!err) {
                // 是否删除源文件
                if (delSource) {
                    extArr.forEach(ext => {
                        delFile(fileName + ext);
                    })
                }
            } else {
                console.log(zip + '压缩失败');
            }
        });
    });
}

/**
 * 获取文件内容
 * @param  {string} fileName 文件名 file.mtl
 */
function getFileContent (fileName) {　　 // 指定encoding会返回一个string，否则返回一个Buffer
    let content = fs.readFileSync(getFullFileName(fileName), { encoding: "utf-8" });
    return content;
}

/**
 * 获取完整文件路径
 * @param  {string} fileName 文件名 file.mtl
 */
function getFullFileName (fileName) {
    return path.join(config.dir, fileName);
}

/**
 * 删除文件
 * @param  {string} fileName 文件名 file.mtl
 */
function delFile (fileName) {
    fs.unlink(getFullFileName(fileName), function (err) {
        if (!!err) {
            console.log('删除文件失败：' + file);
        }
    });
}
```

参考地址:[Node.js使用jszip实现打包zip压缩包](https://www.cnblogs.com/xieqian/p/10023203.html)

#### 修改代码逻辑

`JSUtils.js`

```javascript
const fs = require("fs");
const {GameConst} = require("./GameConst");
const fileMainName = "main";
const fileGameName = "game";
//写入文件，会完全替换之前JSON文件中的内容
function changeJS() {
    //修改main.js
    console.log(`开始修改${fileMainName}.js`)
    try {
        // const dir = "D:\\Build\\mini\\wx\\wechatgame\\engine\\";
        const dir = `${GameConst.wxToolProjectRoot}\\${GameConst.platform}\\${GameConst.subPackageFileName}\\`;
        const pathMain = `${dir}${fileMainName}.js`;
        if (fs.existsSync(pathMain)) {
            let data = fs.readFileSync(pathMain, 'utf8')
            if (data.indexOf('async') === -1) {
                let resultString = data.replace(`"use strict";`, `"use strict";\n \n const {loadSettingsJson,composeSettings,loadSettingsZip} = require("../fastScreen/application");`)
                resultString = resultString.replace(`window.boot = function () {`, `window.boot = async function () { \n  await loadSettingsZip(); \n  await composeSettings(window._CCSettings); \n`)
                if (!resultString) return;
                try {
                    fs.writeFile(pathMain, resultString, () => {
                        console.log(`更改${fileMainName}.js 成功`);
                    })
                } catch (e) {
                    console.log('Invalid Javascript Object Entered entered.');
                }
            } else {
                console.log(`无须修改${fileMainName}.js`)
            }

        }
    } catch (err) {
        console.error(err)
    }


    //修改game.js
    try {
        // const dir = "D:\\Build\\mini\\wx\\wechatgame\\engine\\";
        const dir = `${GameConst.wxToolProjectRoot}\\${GameConst.platform}\\${GameConst.subPackageFileName}\\`;
        const pathGame = `${dir}${fileGameName}.js`;
        if (fs.existsSync(pathGame)) {
            console.log(`开始修改${fileGameName}.js`)
            let gameData = fs.readFileSync(pathGame, 'utf8')
            if (gameData.indexOf('settings') !== -1) {
                let gamedataString = gameData.replace(`require('./src/settings');`, ``)
                try {
                    fs.writeFile(pathGame, gamedataString, () => {
                        console.log(`更改${fileGameName}.js 成功`);
                    })
                } catch (e) {
                    console.log('Invalid Javascript Object Entered entered.');
                }
            } else {
                console.log(`无须修改${fileGameName}.js`)
            }

        }
    } catch (err) {
        console.error(err)
    }
}
exports.changeJS = changeJS;
```

`GameConst.js`

```javascript
exports.GameConst = {
    // url:"D:\\Build\\mini\\js2json2zip",
    ccBuildProjectRoot:"D:\\Build\\mini\\wxbuild",
    wxToolProjectRoot:"D:\\Build\\mini\\wx",
    platform: "wechatgame",
    subPackageFileName: "engine"
};
```

老规矩：最后源码地址（[点击前往](https://github.com/jsroads/mylibs/tree/main/js2json2zip)）

## 首屏优化

关于首屏优化这里给出几个不错的文章，本人就是按照这些步骤操作的。

### 方案一

- [微信小游戏的启动性能优化之首屏渲染](https://developers.weixin.qq.com/community/develop/article/doc/000c86f5ee81b8c05b2bc9cc650013)

### 方案二

1. 先使用 最新版本的 Cocos Creator 构建微信小游戏的包
2. 然后修改为自己 Cocos Creator 可以使用的即可

论坛初步讨论帖子：https://forum.cocos.org/t/topic/115923/18

