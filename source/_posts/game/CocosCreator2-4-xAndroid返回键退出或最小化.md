---
title: CocosCreator2.4.xAndroid返回键退出或最小化
comments: true
categories: game
tags:
  - Cocos
  - Android
abbrlink: 7fbc7c01
date: 2024-07-22 11:31:29
---

游戏开发中，有时候用户点击了返回键（原生`Android`）需要我们对当前的`App`进行最小化或者退出操作，很多`CocosCreator`的开发者，例如笔者，对原生这块儿不是很熟悉，于是就想着写个文档记录一下，也希望帮到同样需求的人。
<!--more-->

## 构建原生Android

本次使用环境

```
NDK路径 /Users/myuser/Library/Android/sdk/ndk/21.4.7075529
Android SDK路径 /Users/myuser/Library/Android/sdk
```

![image](./CocosCreator2-4-xAndroid返回键退出或最小化/c0bbfdac68e611094963561c972803541a801f01_2_689x436.png)

![image](./CocosCreator2-4-xAndroid返回键退出或最小化/4d0c4870d2ef66845104d496a9179704fbc63864_2_328x500.jpeg)

注意如果是`Apple M2（M3）`芯片需要修改 `ndk-build`文件，笔者本地测试过 `NDK 25` 就不需要修改关于`M2` 的修改。![image](./CocosCreator2-4-xAndroid返回键退出或最小化/073a2e80c02969df88f5395aeaa3d11274f71318.png)

![image](./CocosCreator2-4-xAndroid返回键退出或最小化/916bb48ada199ac1fe3599369dcbced4ce31076f.png)

```shell
arch -x86_64 /bin/bash $DIR/build/ndk-build "$@"
```

其他关于 Gradle 和 Gradle 插件的相关知识
[https://developer.android.com/build/releases/gradle-plugin?hl=zh-cn 1](https://developer.android.com/build/releases/gradle-plugin?hl=zh-cn)

## 增加逻辑

### 退出操作

#### 方法一 Android端处理

1. 首先需要让`Activity` 获取焦点 在`Activity`的`onCreate`中的最后，为`SurfaceView`加上`focus`
   `mGLSurfaceView.requestFocus();`

2. `AppActivity.java`路径：`build/jsb-default/frameworks/runtime-src/proj.android-studio/app/src/org/cocos2dx/javascript/AppActivity.java`

![image-20240722115046215](./CocosCreator2-4-xAndroid返回键退出或最小化/image-20240722115046215.png)

```java
//添加焦点
this.getGLSurfaceView().requestFocus();
```

3. 在`Android`工程中，`AppActivity`类里，覆盖如下方法

```java
@Override
public boolean dispatchKeyEvent(KeyEvent event) {
//拦截返回键
if (event.getKeyCode() == KeyEvent.KEYCODE_BACK){
//判断触摸UP事件才会进行返回事件处理
if (event.getAction() == KeyEvent.ACTION_UP) {
		onBackPressed();
}
//只要是返回事件，直接返回true，表示消费掉
return true;
}
return super.dispatchKeyEvent(event);
}

public void onBackPressed() {
// do something…
}
```

#### 方法二 在Javascript端处理

1. 监听系统按键
2. 处理按键信息

```javascript
if (cc.sys.platform == cc.sys.ANDROID) {
    cc.systemEvent.on(cc.SystemEvent.EventType.KEY_DOWN, this.onKeyDown, this);
}
onKeyDown(event) {
    switch (event.keyCode) {
    case cc.macro.KEY.back:
        // 参考步骤3
        //dosomeThing
        break;
    }
}
```

退出游戏：为了防止玩家误点，一般会要求再点一次*后退*才确认退出游戏。退出游戏的话，用 `cc.game.end()`。

简单流程：
1). 添加标记`_toExitGame`来记录首次按键。
2). 首次点击*后退*时，该标记设置为true，发出消息，用于弹出提示“再次点击后退，退出游戏”。
3). 如果3秒内没有再次点击，则重置该标记。

```javascript
if (!this._toExitGame) {
    // 首次点击，重置该标记
    this._toExitGame = true;
    // 发出后退消息，这里监听者来弹出提示：再次点击后退，退出游戏。这里可以替换成自己的消息处理方法
    // this.sendNotification(GAMEEVENTS.APP.TRY_EXIT);
    // 3秒后没有再次按【后退】按钮，则重置该标记
    this.scheduleOnce(() = >{
        this._toExitGame = false;
        // this.label.string = '';
    },3);
} else {
    // 已经点击过一次，则直接退出
    cc.game.end();
    return;
}
```

**注意：测试中，如果发现某些手机不兼容，请使用方法一**

### 最小化App

1. 首先需要让`Activity` 获取焦点 在`Activity`的`onCreate`中的最后，为`SurfaceView`加上`focus`
   `mGLSurfaceView.requestFocus();`
2. `AppActivity.java`路径：`build/jsb-default/frameworks/runtime-src/proj.android-studio/app/src/org/cocos2dx/javascript/AppActivity.java`

3. 具体代码

```java
//添加焦点
this.getGLSurfaceView().requestFocus();
```

![image-20240722115046215](./CocosCreator2-4-xAndroid返回键退出或最小化/image-20240722115046215.png)

4. 在`Android`工程中，`AppActivity`类里，覆盖如下方法

```java
@Override
public boolean dispatchKeyEvent(KeyEvent event) {
//拦截返回键
if (event.getKeyCode() == KeyEvent.KEYCODE_BACK){
//判断触摸UP事件才会进行返回事件处理
if (event.getAction() == KeyEvent.ACTION_UP) {
		//onBackPressed();//退出
  	 moveTaskToBack(true);//最小化
}
//只要是返回事件，直接返回true，表示消费掉
return true;
}
return super.dispatchKeyEvent(event);
}

public void onBackPressed() {
// do something…
}
```

## 总结

通过介绍，学习了如何在`Android`应用中实现退出和窗口最小化的功能。通过调用`onBackPressed`和`moveTaskToBack(true)`方法，我们可以将应用窗口退出或最小化到后台，让应用退出或在后台运行但不占据用户屏幕空间。

## 参考

- [android 窗口最小化](https://blog.51cto.com/u_16213335/10030971) 
- [【分享】Cocos Creator Android 平台监听返回按键无效的解决方案](https://forum.cocos.org/t/topic/115267)
- [安卓监听返回键Unknown keyCode：6](https://forum.cocos.org/t/unknown-keycode-6/76298/7)
- [返回键监听在Android 9 (API 28)后有问题](https://forum.cocos.org/t/android-9-api-28/82215)
- [cocos creator 2.4.5 android 退出返回键](https://forum.cocos.org/t/topic/140553)
- [Cocos Creator 实现安卓按【后退】键退出游戏](https://xmanyou.com/cocos-creator-shi-xian-an-zhuo-an-fan-hui-jian-tui-chu-you-xi/)

