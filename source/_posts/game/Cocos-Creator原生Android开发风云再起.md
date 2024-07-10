---
title: Cocos Creator原生Android开发风云再起
comments: true
categories: game
tags:
  - Cocos
  - Android
abbrlink: 85d041b7
date: 2024-07-10 14:08:29
---

这里是你博客列表显示的摘要文字时隔两年，项目开始重新改到Android赛道上，于是再次开启了Cocos Creator和Android Studio之间的开发徘徊，下面整理一些期间遇到的问题以及解决方案，记录一下，持续更新。
<!--more-->

## 问题

### ERROR: Unknown host CPU architecture: arm64

点击游戏运行预览报错

```shell
[CXX1429] error when building with ndkBuild using /Users/ydc012/TestCase/CCC/NativeDemo/build1/jsb-default/frameworks/runtime-src/proj.android-studio/app/jni/Android.mk: ERROR: Unknown host CPU architecture: arm64

C++ build system [configure] failed while executing:
    /Users/ydc012/Library/Android/sdk/ndk/21.4.7075529/ndk-build \
      NDK_PROJECT_PATH=null \
      APP_BUILD_SCRIPT=/Users/ydc012/TestCase/CCC/NativeDemo/build1/jsb-default/frameworks/runtime-src/proj.android-studio/app/jni/Android.mk \
      NDK_APPLICATION_MK=/Users/ydc012/TestCase/CCC/NativeDemo/build1/jsb-default/frameworks/runtime-src/proj.android-studio/app/jni/Application.mk \
      APP_ABI=arm64-v8a \
      NDK_ALL_ABIS=arm64-v8a \
      APP_PLATFORM=android-16 \
      NDK_OUT=/Users/ydc012/TestCase/CCC/NativeDemo/build1/jsb-default/frameworks/runtime-src/proj.android-studio/app/build/intermediates/cxx/Debug/2h6n6n4c/obj \
      NDK_LIBS_OUT=/Users/ydc012/TestCase/CCC/NativeDemo/build1/jsb-default/frameworks/runtime-src/proj.android-studio/app/build/intermediates/cxx/Debug/2h6n6n4c/lib \
      NDK_TOOLCHAIN_VERSION=clang \
      NDK_MODULE_PATH=/Users/ydc012/TestCase/CCC/NativeDemo/build1/jsb-default/frameworks/cocos2d-x:/Users/ydc012/TestCase/CCC/NativeDemo/build1/jsb-default/frameworks/cocos2d-x/cocos:/Users/ydc012/TestCase/CCC/NativeDemo/build1/jsb-default/frameworks/cocos2d-x/external \
      -j8 \
      NDK_DEBUG=1 \
      APP_SHORT_COMMANDS=false \
      LOCAL_SHORT_COMMANDS=false \
      -B \
      -n
  from /Users/ydc012/TestCase/CCC/NativeDemo/build1/jsb-default/frameworks/runtime-src/proj.android-studio/app

```

#### 问题分析：

1. 官网默认下载的是普通的mac版本，而不是mac arm 版本，然后ndk-build前面加上arch -x86_64

![image-20240710145343861](./Cocos-Creator原生Android开发风云再起/image-20240710145343861.png)

2. 比如这里 需要打开 `/Users/ydc012/Library/Android/sdk/ndk/21.4.7075529`
   ![image-20240710145436488](./Cocos-Creator原生Android开发风云再起/image-20240710145436488.png)

3. 原内容：

```shell
#!/bin/sh
DIR="$(cd "$(dirname "$0")" && pwd)"
$DIR/build/ndk-build "$@"
```

修复后：

```shell
#!/bin/sh
DIR="$(cd "$(dirname "$0")" && pwd)"
arch -x86_64 /bin/bash $DIR/build/ndk-build "$@"
```

