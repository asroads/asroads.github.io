---
title: Typescript写一个可以配置的日志管理器
comments: true
categories: tool
tags:
  - ts
abbrlink: b3783407
date: 2020-01-20 11:30:07
---

在做项目开发的时候，很多时候 都需要打印日志观看我们的结果，或者追踪我们的数据进度，有时候代码正常的时候，不希望看到日志 只有在异常或者 查看错误的时候 才希望有有日志查看。于是下面简单的写了一个日志管理类，可以根据日志级别 控制日志输出的内容。
<!--more-->
其实实现思路也很简单，就是自己定义一个类，写几个方法，让自己的方法 被 系统的日志方法赋值即可

实现如下：

```typescript
/**
 * Created by jsroads on 2020/1/20 . 9:58 上午
 * Note:
 */
export default class TSLog {
    static get i(): TSLog {
        if(!this._i)this._i = new TSLog();
        return this._i;
    }

    private static _i:TSLog;

    private debugLevel = 0;
    log = (message?: any, ...optionalParams: any[]) => {
    };
    info = (message?: any, ...optionalParams: any[]) => {
    };
    warn = (message?: any, ...optionalParams: any[]) => {
    };
    error = (message?: any, ...optionalParams: any[]) => {
    };
    assert = (condition?: boolean, message?: string, ...data: any[]) => {
    };

    constructor() {
        this.debugLevel = 3;
        this.setLogLevel();
    }

    private setLogLevel() {
        if (this.debugLevel === 3) {
            this.log = console.log;
            this.info = console.info;
            this.warn = console.warn;
        }
        if (this.debugLevel === 2) {
            this.info = console.info;
            this.warn = console.warn;
        }
        if (this.debugLevel === 1) {
            this.warn = console.warn;
        }
        if (this.debugLevel !== 0) {
            this.error = console.error;
            this.assert = console.assert;
        }
    }
}
```

调用：

```typescript
TSLog.i.log("hello world");
let zhangSan = {age:18,name:"张三",sex:"1"};
console.log("smile----:", JSON.stringify(zhangSan,["age","name"]));
```

输出结果：

```javascript
hello world
TestCase.ts:138smile----: {"age":18,"name":"张三"}
```

PS 小技巧：**打印的时候 可以用 JSON.stringify 函数 第二个参数 过滤 自己要打印的对象**