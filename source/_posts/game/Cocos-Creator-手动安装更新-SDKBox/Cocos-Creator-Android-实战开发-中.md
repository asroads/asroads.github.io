---
title: Cocos Creator|Android 实战开发(中)
comments: true
categories: game
tags:
  - Cocos
  - Android
abbrlink: 5a097fa7
date: 2020-03-08 19:27:16
---

前面一篇文章简单的介绍了，Cocos Creator 开发Android APK 过程中遇到的一些坑，然后通过网上搜索，自己摸索找到了一些解惑和理解的东西，这篇文章再次带大家继续填坑之路，这些是我后面重新为开发项目中遇到的一些问题。
<!--more-->

下面是这几天遇到的问题：

### 错误：

#### 1. ScriptEngine::onGetStringFromFile ./jsb-dragonbones.js not found, possible missing file.

**原因：**很多做了小游戏的同学，在切换版本的时候很容易入坑，这个其实是开发的时候，导入模块不够的问题，在开发web的时候一般为了包体或者游戏主文件更小，采取了用到哪些，勾选哪些，这样如果照搬到安卓平台，就有了这个报错。

**现象：**

```javascript
2020-03-05 17:30:31.640 7446-7475/org.mykj.train E/jswrapper: ScriptEngine::onGetStringFromFile ./jsb-dragonbones.js not found, possible missing file.
2020-03-05 17:30:31.640 7446-7475/org.mykj.train E/jswrapper: ScriptEngine::runScript script ./jsb-dragonbones.js, buffer is empty!
2020-03-05 17:30:31.640 7446-7475/org.mykj.train E/jswrapper: [ERROR] Failed to invoke require, location: /Users/smile/my/build/train/tt/jsb-default/frameworks/cocos2d-x/cocos/scripting/js-bindings/manual/jsb_global.cpp:292

```

或者：

```javascript
2020-03-06 18:51:27.935 28462-28469/? E/os2d.helloworl: Unable to peek into adb socket due to error. Closing socket.: Connection reset by peer
2020-03-06 18:51:28.584 28462-28497/? E/jswrapper: ScriptEngine::onGetStringFromFile ./assemblers/motion-streak.js not found, possible missing file.
2020-03-06 18:51:28.584 28462-28497/? E/jswrapper: ScriptEngine::runScript script ./assemblers/motion-streak.js, buffer is empty!
2020-03-06 18:51:28.584 28462-28497/? E/jswrapper: [ERROR] Failed to invoke require, location: /Users/smile/my/build/ysx/android/jsb-default/frameworks/cocos2d-x/cocos/scripting/js-bindings/manual/jsb_global.cpp:292
2020-03-06 18:51:28.585 28462-28497/? E/jswrapper: ScriptEngine::onGetStringFromFile ./jsb-dragonbones.js not found, possible missing file.
2020-03-06 18:51:28.585 28462-28497/? E/jswrapper: ScriptEngine::runScript script ./jsb-dragonbones.js, buffer is empty!
2020-03-06 18:51:28.585 28462-28497/? E/jswrapper: [ERROR] Failed to invoke require, location: /Users/smile/my/build/ysx/android/jsb-default/frameworks/cocos2d-x/cocos/scripting/js-bindings/manual/jsb_global.cpp:292

```

图片这样的

![image-20200305173448471](Cocos-Creator-Android-实战开发-中/image-20200305173448471.png)



![image-20200306185203146](Cocos-Creator-Android-实战开发-中/image-20200306185203146.png)

**解决办法：**

![image-20200308194344289](Cocos-Creator-Android-实战开发-中/image-20200308194344289.png)

![image-20200308194421994](Cocos-Creator-Android-实战开发-中/image-20200308194421994.png)

勾选 对应报错 缺失的 模块即可。

#### Android Studio升级3.5后，Instant Run不见了？

有时候看到软件或者编辑器新版本出来了，忍不住要更新尝试新版本的优化成果和新功能，顺便熟悉一波新版本带来的困惑。

打开设置 -> Build,Execution,Deployment -> Debugger -> HotSwap 选中右面的 Enable hot-swap agent for Groovy code

直接搜Instant Run是搜不到的，用HotSwap代替了

参考链接：[Android Studio升级3.5后，Instant Run去哪了？](https://www.jianshu.com/p/bf1c1c594cfa)

#### 安卓签名文件 报警告：

```bash
生成后会提示:
JKS 密钥库使用专用格式。建议使用 "keytool -importkeystore -srckeystore jhy-release-key.jks -destkeystore jhy-release-key.jks -deststoretype pkcs12" 迁移到行业标准格式 PKCS12。
```

签名 生成步骤：

1. 生成签名文件

   ```bash
   keytool -genkey -v -keystore jhy-release-key.jks -keyalg RSA -keysize 2048 -validity 10000 -alias alias_jhy
   ```

   > 输入的密码要记住，其他姓名地区等信息随便填吧，最好还是记住，成功之后在主目录下就生成了`jhy-release-key.keystore`文件，命令中jhy-release-key.keystore是生成文件的名字，`alias_jhy`是别名，随便起但是要记住，一会签名要用到，其他信息如加密、有效日期等就不说了，无需改动。
   >
   > 生成后会提示:
   >  `JKS 密钥库使用专用格式。建议使用 "keytool -importkeystore -srckeystore jhy-release-key.jks -destkeystore jhy-release-key.jks -deststoretype pkcs12" 迁移到行业标准格式 PKCS12。`

2.  执行命令：

   ```bash
   keytool -importkeystore -srckeystore jhy-release-key.jks -destkeystore jhy-release-key.jks -deststoretype pkcs12
   ```

   > 执行结果：`Warning: 已将 "jhy-release-key.jks" 迁移到 Non JKS/JCEKS。将 JKS 密钥库作为 "jhy-release-key.jks.old" 进行了备份。`

   参考链接：[Android打包流程](https://www.jianshu.com/p/7d2def9b93a8)

### 参考

- [cocosCreator 构建IOS,j s调用原生方法没有问题！jsb-webview.js not found](https://forum.cocos.org/t/cocoscreator-ios-j-s-jsb-webview-js-not-found/83662)

- [Android Studio升级3.5后，Instant Run去哪了？](https://www.jianshu.com/p/bf1c1c594cfa)

- [Android打包流程](https://www.jianshu.com/p/7d2def9b93a8)

  