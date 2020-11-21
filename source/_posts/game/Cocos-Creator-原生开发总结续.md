---
title: Cocos Creator 原生开发总结续
comments: true
categories: game
tags:
  - Cocos
  - iOS
  - Android
abbrlink: ddcbf515
date: 2020-11-15 16:42:06
---

之前以为和原生交互比较少，就写了几篇文章后面越来越多，就遇到了很多问题，于是在写一个这几天遇到的问题吧，关于数据转换和一些小技巧的。
<!--more-->

## OC

### 解决NSArray、NSDictionary直接打印中文出现乱码的问题

在iOS开发中，经常需要查看数组中得元素是否是自己想要的，但是苹果并没有对直接打印数组中得中文作处理，直接打印就会出现一堆很讨厌的东西

### ALAlertView

地址:https://github.com/alanshen0118/ALAlertView

#### 对外接口

```objective-c
+ (instancetype)alertView;

/**
 *  类方法  提示框（只显示消息）
 *
 *  @param message 消息
 */
+ (void)showMessage:(NSString *)message;

/**
 *  类方法  提示框（只显示标题和消息）
 *
 *  @param title   标题
 *  @param message 消息
 */
+ (void)showAlertViewWithTitle:(NSString *)title message:(NSString *)message;

/**
 *  类方法  提示框（只显示标题和消息）
 *
 *  @param title   标题
 *  @param message 消息
 *  @param dismissCallBack 确定回调
 */
+ (void)showAlertViewWithTitle:(NSString *)title message:(NSString *)message dismissCallBack:(CancelCallBack)dismissCallBack;

/**
 *  提示框基础方法
 *
 *  @param title             标题
 *  @param message           消息
 *  @param cancelButtonTitle 取消按钮标题
 *  @param cancelCallBack    取消按钮回调
 *  @param otherCallBack     其他按钮回调
 *  @param otherButtonTitles 其他按钮
 */
- (void)showAlertViewWithTitle:(NSString *)title message:(NSString *)message cancelButtonTitle:(NSString *)cancelButtonTitle cancelCallBack:(CancelCallBack)cancelCallBack otherCallBack:(OtherCallBack)otherCallBack otherButtonTitles:(NSString *)otherButtonTitles, ... NS_REQUIRES_NIL_TERMINATION;

/**
 *  提示框（只显示消息）
 *
 *  @param message 消息
 */
- (void)showMessage:(NSString *)message;

/**
 *  提示框（只显示标题和消息）
 *
 *  @param title   标题
 *  @param message 消息
 */
- (void)showAlertViewWithTitle:(NSString *)title message:(NSString *)message;

/**
 *  提示框（只显示标题和消息）
 *
 *  @param title   标题
 *  @param message 消息
 *  @param dismissCallBack 确定回调
 */
- (void)showAlertViewWithTitle:(NSString *)title message:(NSString *)message dismissCallBack:(CancelCallBack)dismissCallBack;
```

#### 示例

```objective-c
//
//  ViewController.m
//  ALAlertViewDemo
//
//  Created by alan on 8/22/16.
//  Copyright © 2016 bjdv. All rights reserved.
//

#import "ViewController.h"
#import "ALAlertView.h"

@interface ViewController ()

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
}

- (void)viewDidAppear:(BOOL)animated {
    [super viewDidAppear:animated];
    [[ALAlertView alertView] showMessage:@"建议在界面完成加载后调用"];
}
- (IBAction)e__message:(id)sender {
    XXALERT(@"消息");
    
//    [ALAlertView showMessage:@"消息"];
//    [[ALAlertView alertView] showMessage:@"消息"];
}

- (IBAction)e__messageAndTitle:(id)sender {
    
    [[ALAlertView alertView] showAlertViewWithTitle:@"标题" message:@"消息"];
}

- (IBAction)e__mutiButtons:(id)sender {
    [[ALAlertView alertView] showAlertViewWithTitle:@"标题" message:@"消息" cancelButtonTitle:@"取消" cancelCallBack:^{
        NSLog(@"cancel回调");
    } otherCallBack:^(NSInteger buttonIndex) {
        NSLog(@"第%zi个按钮回调", buttonIndex + 1);
    } otherButtonTitles:@"按钮1", @"按钮2", @"按钮3", nil];
}

- (IBAction)e__mutiAlertWithoutOverlay:(id)sender {
    [ALAlertView showAlertViewWithTitle:nil message:@"第一条信息" dismissCallBack:^{
        [[ALAlertView alertView] showMessage:@"第二条消息"];
    }];
    
}

- (void)didReceiveMemoryWarning {
    [super didReceiveMemoryWarning];
    // Dispose of any resources that can be recreated.
}

@end
```

### 退出App

```objective-c
exit(0);
```

其他相关

```objective-c
abort();
```

苹果退出有两种方式：exit(0);和abort();

exit和abort都是终止程序执行退出的处理函数，其中exit是正常退出，abort是异常退出，退出时会输出错误信息，然后再调用exit退出。

用户体验来说，exit就像是程序崩溃了，直接退出程序。abort就像是点击了home键，有过渡动画，一般我们在使用的时候会选择abort();

但是苹果审核好像是不支持这样去操，会造成很不好的用户体验，有很大的机率会拒绝审核，但是查看官方文档用到exit(0)；

方法:
 在plist添加屬性Application does not run in background = YES，调用exit(0)

### iOS App内评分

苹果在iOS 10.3中增加了App评分的新API，用户可以直接在App内评分，而不用跳转到App Store。
使用非常简单，只有一行代码，但也说明了它没有任何可定制性。
此API使用前需要 

```objective-c
    #import <StoreKit/StoreKit.h>:
    if (@available(iOS 10.3, *)) {
            [SKStoreReviewController requestReview];
    }
```

```objective-c
/**
 * 只能评分，不能编写评论
 * 有次数限制，一年只能使用三次
 * 使用次数超限后，需要跳转appstore
 */
- (IBAction)systemComentBtnAction:(UIButton *)sender {

if([SKStoreReviewController respondsToSelector:@selector(requestReview)]) {// iOS 10.3 以上支持
    //防止键盘遮挡
    [[UIApplication sharedApplication].keyWindow endEditing:YES];
    [SKStoreReviewController requestReview];
} 
}
```

**注意：**
苹果官方说不能骚扰用户，因此使用此API有非常严格的限制。
按苹果规定，**每台设备、每个App、每年只能弹出3次**该提示窗口，如果超过这个次数，则什么都不会显示。

#### Deep Link

大家应该都知道跳转到App Store的方法：

```objective-c
NSString *appURL = @"https://itunes.apple.com/cn/app/idAppID";
[[UIApplication sharedApplication] openURL:[NSURL URLWithString:appURL]];
```

下面介绍一种可以直接跳转到评分页面的方法。这个方法其实在上述新API的注释中苹果已经提到，他们称为“Deep Link”。使用方式与上面的方法一致，只是在URL后面拼接一个query params：`action=write-review`。
 下面是完整代码：

```objective-c
NSString *appURL = @"https://itunes.apple.com/cn/app/idAppID?action=write-review";
[[UIApplication sharedApplication] openURL:[NSURL URLWithString:appURL]];
```

作者：乌鸢
链接：https://www.jianshu.com/p/4f9fe2e1879e
来源：简书

作者：贝尔特伦
链接：https://www.jianshu.com/p/e3f6d584d679
来源：简书

### iOS字符串转JSON

```objective-c

+(NSDictionary*)dictionaryWithJsonString:(NSString*)jsonString {
    
    if(jsonString ==nil) {
        
        return nil;
        
    }
    
    NSData*jsonData = [jsonString dataUsingEncoding:NSUTF8StringEncoding];
    
    NSError*err;
    
    NSDictionary*dic = [NSJSONSerialization JSONObjectWithData:jsonData
                        
                                                       options:NSJSONReadingMutableContainers
                        
                                                         error:&err];
    
    if(err) {
        
        NSLog(@"json解析失败：%@",err);
        
        return nil;
    }
    
    NSMutableDictionary*newdict=[[NSMutableDictionary alloc]init];
    
    for(NSString*keys in dic)
        
    {
        
        if(dic[keys]==[NSNull null])
            
        {
            
            [newdict setObject:@" "forKey:keys];
            
            continue;
            
        }
        
        [newdict setObject:[NSString stringWithFormat:@"%@",dic[keys]]forKey:keys];
        
    }
    return newdict;
    
}


```



## Android

### Android 使用 JavaScript 调用 Java 方法

在 Android 应用中，Cocos 引擎的渲染和 JS 的逻辑是在 GL 线程中进行的，而 Android 本身的 UI 更新是在 App 的 UI 线程进行的，所以如果我们在 JS 中调用的 Java 方法有任何刷新 UI 的操作，都需要在 UI 线程进行。

参考 ：[如何在 Android 平台上使用 JavaScript 直接调用 Java 方法](https://docs.cocos.com/creator/manual/zh/advanced-topics/java-reflection.html)

### 退出

- **KillProcess()：**
  可以杀死当前应用活动的进程，这一操作将会把所有该进程内的资源（包括线程全部清理掉）。当然，由于ActivityManager时刻监听着进程，一旦发现进程被非正常Kill，它将会试图去重启这个进程。

```css
android.os.Process.killProcess(android.os.Process.myPid());
```

- **System.exit()：**
  Java中结束进程的方法，调用它将关闭当前的JVM虚拟机。

```cpp
System.exit(0);//正常退出
System.exit(1);//非正常退出
```

> **KillProcess()** 和 **System.exit()**，许多人都使用过，当你栈里只有一个Activity的时候，这个措施是行之有效的。但当关闭多个Activity的时候，栈里有多个Activity时，这两个方法就不起作用了。
> 因为通过杀进程方式退出，会被系统认为异常退出，会保存应用的一些状态信息比如Activity运行栈，然后会恢复这个应用。当恢复一个Android应用程序时，会先从栈里面移除异常的Activity，相当于Back键操作。


链接：https://www.jianshu.com/p/1adb4a6b8618
来源：简书

### JSONObject

##### net.sf.json.JSONObject

https://sourceforge.net/projects/json-lib/

##### org.json.JSONObject

系统自带的 需要 

```java
JSONObject paramsObj = new JSONObject(paramDataStr);
```

示例:

```java
private void parseJSONWithJSONObject(String JsonData) {
    try
    {
        JSONArray jsonArray = new JSONArray(jsonData);
        for (int i=0; i < jsonArray.length(); i++)    {
            JSONObject jsonObject = jsonArray.getJSONObject(i);
            String id = jsonObject.getString("id");
            String name = jsonObject.getString("name");
            String version = jsonObect.getString("version");
            System.out.println("id" + id + ";name" + name + ";version" + version);
        }
    }
    catch (Exception e)
    {
        e.printStackTrace();
    }
```

### 签名文件.keystore 和 .jks

参考地址:[签名文件.keystore 和 .jks](https://my.oschina.net/Agnes2017/blog/1824614)

### 安卓播放广告时候 切到后台 返回后广告不见了

解决办法:
修改  AndroidManifest.xml 里面主 Activity 启动模式为 singleTop

```xml
     <activity
                android:name="org.cocos2dx.javascript.AppActivity"
                android:screenOrientation="portrait"
                android:configChanges="orientation|keyboardHidden|screenSize|screenLayout"
                android:label="@string/app_name"
                android:theme="@android:style/Theme.NoTitleBar.Fullscreen"
                android:resizeableActivity="false"
                android:launchMode="singleTop">
```

参考地址:[creator接了穿山甲激励视频，切后台广告会关闭怎么处理？](https://forum.cocos.org/t/creator/92318)

## Cocos Creator 

### cc.blink 对应cc,tween如何使用？

```javascript
let blinkAction = cc.blink(0.1, 1);
this.node.runAction(blinkAction);
```

在cc.tween如何使用

```javascript
cc.tween(this.node)
    .blink(0,1, 1)
    .start();
```

参考地址:[请问闪烁 cc.blink 对应cc,tween如何使用？](https://forum.cocos.org/t/cc-blink-cc-tween/91499)

### JavaScriptJavaBridge::CallInfo isn’t valid!

java里接口定义错误，记得要加static

```java
public static void loginWithWX()
```

如果方法名加了static,并且方法名字和参数都没有写错，并且你的方法不是写在AppActivity里面的话，那就考虑是不是release包的代码混淆问题了。cocos打包的模版里面在proguard-rules.pro里自动加了排除混淆的代码
-keep public class org.cocos2dx.** { *; }
-dontwarn org.cocos2dx.**
如果你的方法在其他类里面，记得在proguard-rules.pro给你的包名也加上这样的配置。

## 在线工具

在线EXCEL转JSON工具

https://uutool.cn/excel2json/

在线JSON转EXCEL

https://uutool.cn/json2excel/

## 参考

- [Android安卓 Activity四种启动模式(launchMode) standard, singleTop, singleTask, singleInstance](https://blog.csdn.net/qq_44861716/article/details/104358437)
- [creator原生项目接入UnityAds，激励视频时双进程](https://forum.cocos.org/t/creator-unityads/80430)



