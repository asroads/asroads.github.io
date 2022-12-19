---
title: 字节小游戏接入友盟SDK
comments: true
categories: game
tags:
  - 友盟
  - 小游戏
abbrlink: fc1ba9c0
date: 2022-12-09 17:58:42
---

2021年做游戏项目的时候，接过很多家的SDK，其中有快手、腾讯、Ohayoo和一家国外的SDK，数据统计的也接过好几家，如热云等，这两天忽然有个接入[友盟](https://www.umeng.com/)的需求，于是接完后，开始记录一下接入的过程。
<!--more-->

![image-20221209180430342](%E5%AD%97%E8%8A%82%E5%B0%8F%E6%B8%B8%E6%88%8F%E6%8E%A5%E5%85%A5%E5%8F%8B%E7%9B%9FSDK/image-20221209180430342.png)

虽然字节小游戏有自己的数据统计方法和平台分析，但是有个缺点是无法实时统计数据，特别是有实时统计分析需求的自然而然要找一家这样的公司，恰好友盟具备了这个实时统计分析。

[友盟](https://www.umeng.com/)是一家数据统计分析公司，旗下的产品可以接入多个平台，其中有原生，小游戏和网站等。

可以具体可以参考：

![image-20221209180559395](%E5%AD%97%E8%8A%82%E5%B0%8F%E6%B8%B8%E6%88%8F%E6%8E%A5%E5%85%A5%E5%8F%8B%E7%9B%9FSDK/image-20221209180559395.png)

[示例代码Demo](https://gitee.com/organizations/umengplus/projects)  官方例子里没有 字节的 我们可以参考 微信小游戏的即可： [wx-game](https://gitee.com/umengplus/mp-demos/tree/master/wx-game)

下面 简单的说一下整个步骤：

## 步骤

### 下载sdk

1. 首先我们打开网站首页 [点击前往](https://www.umeng.com/)

![image-20221209181024012](%E5%AD%97%E8%8A%82%E5%B0%8F%E6%B8%B8%E6%88%8F%E6%8E%A5%E5%85%A5%E5%8F%8B%E7%9B%9FSDK/image-20221209181024012.png)

2. 找到 【开发者中心】--->【SDK】按照页面选择 自己的端 然后选择平台 勾选协议，下载SDK

   ![image-20221209181210474](%E5%AD%97%E8%8A%82%E5%B0%8F%E6%B8%B8%E6%88%8F%E6%8E%A5%E5%85%A5%E5%8F%8B%E7%9B%9FSDK/image-20221209181210474.png)

3. SDK下载成功后，如下图所示：

![image-20221209181409958](%E5%AD%97%E8%8A%82%E5%B0%8F%E6%B8%B8%E6%88%8F%E6%8E%A5%E5%85%A5%E5%8F%8B%E7%9B%9FSDK/image-20221209181409958.png)

![image-20221209181431754](%E5%AD%97%E8%8A%82%E5%B0%8F%E6%B8%B8%E6%88%8F%E6%8E%A5%E5%85%A5%E5%8F%8B%E7%9B%9FSDK/image-20221209181431754.png)

uma.min.js  这个就是我们要用的 代码类库。

### 注册账号申请游戏

[小程序统计 U-MiniProgram](https://developer.umeng.com/docs/147615/cate/147615) 文档下面 找到 当前的平台文档：

![image-20221209181650500](%E5%AD%97%E8%8A%82%E5%B0%8F%E6%B8%B8%E6%88%8F%E6%8E%A5%E5%85%A5%E5%8F%8B%E7%9B%9FSDK/image-20221209181650500.png)

字节小游戏 接入文档 [点击前往](https://developer.umeng.com/docs/147615/detail/181348)

其中文档里面有详细的步骤，另外一直再更新，我这里就不在转述，大家把这个页面细看一遍，有些注意事项，下面看看。

## 注意事项

#### 引入类库注意事项

1. 游戏内的引入，文档是这么写的

```javascript
var uma = require('./utils/uma.min');

uma.init({
  appKey:'YOUR_UMENG_APPKEY',//由友盟分配的APP_KEY
  autoGetOpenid:true,// 是否需要通过友盟后台获取openid或匿名openid，如若需要，请到友盟后台设置appId及secret
  debug:true,//是否打开调试模式
  uploadUserInfo:true// 自动上传用户信息，设为false取消上传，默认为false
})
```

我这里 稍有改变：

原来说的 `game.js` 前面写法：

```javascript
var uma = require('./utils/uma.min');
```

我的`game.js`写法：

```javascript
"use strict";

require('adapter-min.js');

__globalAdapter.init();

require('cocos/cocos2d-js-min.js');

__globalAdapter.adaptEngine();

require('./ccRequire');

require('./src/settings'); // Introduce Cocos Service here

window.uma = require('utils/uma.min')//注意如果放到前面第一行，真机下黑屏具体原因不详，这里不多探究

require('./main'); // TODO: move to common
// Adjust devicePixelRatio


cc.view._maxPixelRatio = 4;

if (cc.sys.platform !== cc.sys.BYTEDANCE_GAME_SUB) {
  // Release Image objects after uploaded gl texture
  cc.macro.CLEANUP_IMAGE_CACHE = true;
}

window.boot();
```

其实我后面测试 这个 全局的`uma` 和 `tt.uma` 是同一个对象，所以有时候文档写 `uma`下的方法有时候写`tt.uma`下的方法 其实是同一个对象的用法。

#### 自定义事件

一般来说我们不会使用官方的自带的那些模板事件，都是自定义事件，关于如何使用自定义事件，这里可以参考

**小程序自定义事件文档**  [点击前往](https://developer.umeng.com/docs/147615/detail/207197)  文档虽然是小程序的 但是小游戏用法一样。大家要看一下注意事项

```javascript
trackEvent('事件ID',{'属性1':'属性值1','属性2':'属性值2'});
// 字符型属性值
trackEvent('ViewProductDetails',{'Category':'家电','ItemName':'西门子冰箱'});
// 数值型属性值
trackEvent('Pay',{'PayAmount':6999});
```

总的来说，就是事件ID是**字符串类型**，后面是 `Javascript` 对象，但是**对象的属性值仅支持字符串和数值两种类型**，不支持其他类型。

另外就是要记得在后台自己添加对应的事件，否则无法统计成功！！！

https://databank.umeng.com/sdc/manage

![image-20221209183435944](%E5%AD%97%E8%8A%82%E5%B0%8F%E6%B8%B8%E6%88%8F%E6%8E%A5%E5%85%A5%E5%8F%8B%E7%9B%9FSDK/image-20221209183435944.png)

如果事件触发次数一直为0，需要检查一下，自己选择 的天数是不是今日，我就被这个坑了一个多小时。

![image-20221209183540026](%E5%AD%97%E8%8A%82%E5%B0%8F%E6%B8%B8%E6%88%8F%E6%8E%A5%E5%85%A5%E5%8F%8B%E7%9B%9FSDK/image-20221209183540026.png)

顺便提醒大家 也看看 这个常见问题，其实很多疑惑的地方瞬间就明白了，不得不说他们家的文档是如何设计的，真的很奇怪，重要问题的答案都藏得很深。

![image-20221209183720748](%E5%AD%97%E8%8A%82%E5%B0%8F%E6%B8%B8%E6%88%8F%E6%8E%A5%E5%85%A5%E5%8F%8B%E7%9B%9FSDK/image-20221209183720748.png)

## 遇到的问题

日志中打印这个：

```bash
[UMENG]--获取id标识失败，暂缓发送
```

解决答案 **[SDK集成，获取id标识失败怎么办？](https://developer.umeng.com/docs/147615/detail/250223)**

![image-20221209184242546](%E5%AD%97%E8%8A%82%E5%B0%8F%E6%B8%B8%E6%88%8F%E6%8E%A5%E5%85%A5%E5%8F%8B%E7%9B%9FSDK/image-20221209184242546.png)



以上是小游戏的整个步骤，下面是一个新的需求，同事要统计一下 导出的 web 测试包 每天有多少人访问。于是新的需求就出现了，对H5 页面不是很擅长的我，自然而然想到了 用友盟接入，下面是简要步骤。

## web网页统计访问

后面接到一个需求要统计 web 页面访问次数，于是想到了 用这个 友盟去统计

1. 首先外面新建一个 H5 类型的 小程序统计

![image-20221220013527706](%E5%AD%97%E8%8A%82%E5%B0%8F%E6%B8%B8%E6%88%8F%E6%8E%A5%E5%85%A5%E5%8F%8B%E7%9B%9FSDK/image-20221220013527706.png)

2. 注册成功后按照步骤注入代码

> ## 2.1 页面曝光埋点
>
> ## sendPV
>
> sendPV 方法将发送一条页面 PV 日志，其 API 定义如下：
>
> ```javascript
> 复制const {aplus_queue} = window;
> aplus_queue.push({
>  action: 'aplus.sendPV',
>   arguments: [pageEventConfig, userData]
> });
> ```
>
> 其中
>
> - pageEventConfig 为页面事件的配置，只需要写死{ is_auto: false } 即可
> - userdata 为本次页面事件的扩展参数，其取值为一个 JSON 对象(平铺的简单对象，不能多层嵌套)
>
> ```javascript
> 复制const {aplus_queue} = window;
> aplus_queue.push({
>  action: 'aplus.sendPV',
>   arguments: [{
>       is_auto: false
>   }, {
>       param1： 111，
>       param2: '222'
>   }]
> });
> ```
>
> 效果如： ![2](%E5%AD%97%E8%8A%82%E5%B0%8F%E6%B8%B8%E6%88%8F%E6%8E%A5%E5%85%A5%E5%8F%8B%E7%9B%9FSDK/p301765.png)
>
> **警告**
>
> **：sdk 默认提供自动pv的能力；但若metaInfo配置信息中aplus-waiting等于MAN时，此时pv的发送时机改为由开发者自己控制，特别是当您的H5应用是一个单页应用时，您必须通过调用sendPV来发送页面曝光事件；pv事件一定要发送，否则影响新增、活跃、分享回流等指标的计算！！**
>
> 
>
> ## 2.2 页面点击事件
>
> record 用于发送一条事件日志，其 API 定义如下：
>
> ```javascript
> 复制const {aplus_queue} = window;
> aplus_queue.push({
>  action: 'aplus.record',
>   arguments: [eventCode, eventType, eventParams]
> });
> ```
>
> 其中，
>
> - eventCode：事件ID 或 事件编码
> - eventType：'CLK'
> - eventParams 为本次事件中上报的事件参数。其取值为一个JSON对象（平铺的简单对象，不能多层嵌套）
> - 调用 record api 上报参数时，该次赋值仅对该条事件有效
> - 保留属性：uid, aplus, spm-url, spm-pre, spm-cnt, pvid, *dev_id,* anony_id, *user_id,* user_nick, _session_id
>
> ```javascript
> 复制const {aplus_queue} = window;
> //一个简单的demo
> aplus_queue.push({
>  action: 'aplus.record',
>   arguments: ['yourTrackerEventCode', 'CLK', {
>      param1: '111',
>      param2: '222',
>      param3: 333
>    }]
> });
> ```
>
> 效果如下：
>
> ![3](%E5%AD%97%E8%8A%82%E5%B0%8F%E6%B8%B8%E6%88%8F%E6%8E%A5%E5%85%A5%E5%8F%8B%E7%9B%9FSDK/p301766.png)
>
> ## 2.3 元数据设置
>
> 用于变更 SDK 的默认设置
>
> ```javascript
> 复制const {aplus_queue} = window;
> aplus_queue.push({
>  action: 'aplus.setMetaInfo',
>   arguments: [metaName, metaValue, mode]
> });
> ```
>
> 其中：
>
> - metaName 为可配置的元数据项，可配置的 meta项见下文列表
> - metaValue 为对应的元配置项取值
> - mode 为模式，其取值为枚举值：
>
> - - "OVERWRITE": 覆盖模式，原 meta 值将直接被覆盖，注意对于取值为单值的 meta 只有覆盖模式
>     - 默认值
>   - "APPEND": 追加模式，适用于 meta 取值为数组对象的 meta，追加模式下原取值保留
>
> 用于动态获取 SDK 的当前配置
>
> ```javascript
> aplus.getMetaInfo(metaName);
> ```
>
> 目前支持的 metaName 及 metaValue见，**附表1**
>
> ## 2.4 上报时机
>
> 有些信息如用户id、用户昵称等是异步获取的，这种情况下如果您需要等待异步信息获取成功后再上报日志，sdk这边提供了设置元配置信息_hold = BLOCK 来阻塞上报，再时机成熟时通过更新元配置信息_hold=START来开启上报
>
> ```javascript
> 复制const {aplus_queue} = window;
> //如采集用户信息是异步行为，需要先阻止SDK上报，设置BLOCK埋点
> aplus_queue.push({
>  action: 'aplus.setMetaInfo',
>   arguments: ['_hold', 'BLOCK'] 
> });
> 
> ...异步耗时流程
> 
>  // 因为采集用户信息是异步行为，故需要先设置BLOCK，再设置START
>  // 设置_hold=START后，事先被block住的日志会携带上用户信息逐条发出
> aplus_queue.push({
>  action: 'aplus.setMetaInfo',
>   arguments: ['_hold', 'START'] 
> });
> ```
>
> ## 

3. 按照上面 前面操作的 **页面曝光埋点** 引入即可 
4. 等几分钟 查看数据 下面是 页面曝光 和 自定义埋点 

![image-20221220014003380](%E5%AD%97%E8%8A%82%E5%B0%8F%E6%B8%B8%E6%88%8F%E6%8E%A5%E5%85%A5%E5%8F%8B%E7%9B%9FSDK/image-20221220014003380.png)

![image-20221220014036111](%E5%AD%97%E8%8A%82%E5%B0%8F%E6%B8%B8%E6%88%8F%E6%8E%A5%E5%85%A5%E5%8F%8B%E7%9B%9FSDK/image-20221220014036111.png)

5. 核心代码：

```javascript
  const {aplus_queue} = window;
  aplus_queue.push({
    action: 'aplus.sendPV',
    arguments: [{
      is_auto: false
    }, {
      param1: 111,
      param2: '222'
    }]
  });
```

```javascript
console.log("测试打点！！！")

const {aplus_queue} = window;
//一个简单的demo
aplus_queue.push({
    action: 'aplus.record',
    arguments: ['login', 'CLK', {
        param1: '111',
        param2: '222',
        param3: 333
    }]
});
```

6. 以上 就是 web 页面 使用 友盟统计的简要步骤。

## 总结

其实接入过程不是很复杂，但是因为官网的一些文档杂乱，和小游戏文档的部分缺失，导致一些新手接入的时候，会找不到东西南北，很多时候，接入小游戏可以横向参考微信小游戏，或者纵向参考小程序的做法，基本都可以找到解决方案，另外就是必须登录账号去看文档，很多文档操作的关键步骤，藏在管理台的侧边栏，这个很尴尬。