---
title: 函数防抖与节流和TypeScript装饰结合
comments: true
categories: other
tags:
  - ts
abbrlink: 21c1b53
date: 2021-07-02 16:30:50
---

很多时候，用户在交互过程中有时候操作需要异步处理，或者需要等待，不能让用户频繁交互，或者防止客户端和服务器频繁交互都会做出一些限制，其中常用的操作是函数防抖和函数节流，对于函数做一些处理后，让用户的行为效果延迟发送，或者在一定期间内只能发送一次。Typescript的函数是支持装饰器的，下面就介绍一下如何通过装饰器来让函数防抖和节流。
<!--more-->

## 防抖Debounce和节流throttle

函数防抖和函数节流：优化高频率执行js代码的一种手段，js中的一些事件如浏览器的`resize`、`scroll`，鼠标的`mousemove`、mouseover，input输入框的keypress等事件在触发时，会不断地调用绑定在事件上的回调函数，极大地浪费资源，降低前端性能。为了优化体验，需要对这类事件进行调用次数的限制。

首先我们介绍函数防抖：

### 函数防抖（`debounce`）

**防抖概念：**在事件被触发n时间段后再执行回调，如果在这n时间段内又被触发，则重新计时。就是将多次高频操作优化为只在最后一次执行（某个函数在某段时间内，无论触发了多少次回调，都只执行最后一次）

**防抖原理**：是维护一个计时器，在规定的`delay`时间后触发函数，但是在`delay`时间内再次触发的话，就会取消之前的计时器而重新设置。**这样一来，只有最后一次操作能被触发。**

```javascript
function debounce(fn, delay) {
  var timer = null;
  return function() {
     // 清除已存在的定时器
     timer && clearTimeout(timer)
     timer = setTimeout(function() {
        fn.apply(this)
     }, delay)
  }
}
```

### 函数节流（`throttle`）

**节流概念：**节流就是每隔一段时间后执行一次，也就是降低频率，将高频操作优化成低频操作。简单说：每隔一段时间，只执行一次函数。

**节流原理：** 有定时器 版本 和 世界戳版本  

- 定时器版本的节流函数其重点是利用闭包保存`timer`变量
- 时间戳版本的节流函数重点是利用闭包保存上一次的时间`previous`

定时器

```javascript
// 定时器版本
function throttle(fn, wait) {
    let timer = null;
    return function(...args) {
        if (!timer) {
            timer = setTimeout(() => {
                fn.apply(this, args);
                timer = null;
            }, wait)
        }
    }
}
```

时间戳

```javascript
// 时间戳版本
function throttle(fn, wait) {
    // 上一次执行时间
    let previous = 0;
    return function(...args) {
        // 当前时间
        let now = +new Date();
        if (now - previous > wait) {
            previous = now;
            fn.apply(this, args);
        }
    }
}
```

## TypeScript装饰器

TypeScript 中的装饰器使用 @expression 这种形式，expression 求值后为一个函数，**它在运行时被调用，被装饰的声明信息会被做为参数传入。**

装饰者模式就是动态的给类或对象增加功能的设计模式，TypeScript 中的装饰器可以被附加到类声明、方法、 访问符(getter/setter)、属性和参数上。

**命令行** 编译文件时：

```bash
tsc --target ES5 --experimentalDecorators mytest.ts
```

配置文件 **tsconfig.json**

```json
{
    "compilerOptions": {
        "target": "ES5",
        "experimentalDecorators": true
    }
}
```

装饰器实际上就是一个函数，在使用时前面加上 **@** 符号，写在要装饰的声明之前，多个装饰器同时作用在一个声明时，可以写一行或换行写：

```typescript
// 装饰器换行写
@testA
@testB
declaration

//装饰器写一行
@testA @testB ...
declaration
```

作用在类的方法上，有静态方法和原型方法。作用在静态方法上，装饰器函数接收的是类构造函数；作用在原型方法上，装饰器函数接收的是原型对象。装饰器器函数（方法）需要保留(`target`,`key`,`descriptor`)三个参数---原型对象、方法名、描述对象。

下面写个例子：

```typescript
function throttle(limit: number = 0) {
    let flag = true
    return (target: any, key: string, descriptor: any) => {
        let func = descriptor.value
        descriptor.value = async (...args: any[]) => {
            if(!flag) return 
            flag = false
            try {
                func.apply(this, ...args)
            } catch (error) {
                console.log(error)    
            }
            if (!limit) return flag = true
            setTimeout(() => {
                flag = true
            }, limit);
        }
    }
}

class Test{
    @throttle(1000)
    fn() {
      console.log('fn')
    }
}

const test = new Test()
setInterval(test.fn,50);
```

#### 装饰器版本

话不多说，最后看一下最终代码：

```typescript
/**
 * 用于操作函数防抖，防抖就是将多次高频操作优化为只在最后一次执行
 * 某个函数在某段时间内，无论触发了多少次回调，都只执行最后一次
 * @param wait 延时ms
 * @param immediate 首次否直接运行
 * @constructor
 */
export const Debounce = (wait: number, immediate: boolean = false) => {
    return (target: any, key: string, descriptor: any) => {
        let timer = null;
        const fn = descriptor.value;
        descriptor.value = async (...args: any[]) => {
            // 立即执行的功能（timer为空表示首次触发）
            if (immediate && !timer) {
                fn.apply(this, args);
            }

            // 有新的触发，则把定时器清空
            timer && clearTimeout(timer);
            // 重新计时
            timer = setTimeout(() => {
                fn.apply(this, args);
            }, wait);
        };
        return descriptor;
    };
};
/**
 * 用于操作函数节流，节流就是每隔一段时间后执行一次，
 * 也就是降低频率，将高频操作优化成低频操作。
 * @param wait 延时ms
 * @constructor
 */
export const Throttle = (wait: number) => {
    return (target: any, key: string, descriptor: any) => {
        let timer = null;
        let fn = descriptor.value;
        descriptor.value = async (...args: any[]) => {
            if (!timer) {
                timer = setTimeout(() => {
                    fn.apply(this, args);
                    timer = null;
                }, wait)
            }
        };
    };
};
```

游戏内使用：

```typescript
 @Debounce(2000)
    private testTouchHandler(touchDebounce,data){
        console.log("-----------Debounce")
        console.log("Debounce:" + data + ":"  +JSON.stringify(Date.now()));
    }

    @Throttle(2000)
    private testTouchHandler2(touchThrottle,data){
        console.log("-----------Throttle")
        console.log("Throttle:" +data+  ":"  +JSON.stringify(Date.now()));
    }
```

以上代码看起来整洁，而且实现了函数防抖和函数节流。

#### 动态添加和取消版本

最后再放出一个可以动态添加和取消的防抖和节流的代码：

```typescript
 /**
     * @description: 防抖函数
     * @param {Function} func 回调函数
     * @param {Number}  wait 等待时间 ms
     * @param {Boolean} immediate 是否立即执行
     * @return: result func返回的结果
     */
    public static debounce(func: Function, wait: number = 100, immediate: boolean = false) {
        let timeoutID: number = 0, result: any;
        let debounced: FunDebounceOrThrottle = function () {
            clearTimeout(timeoutID);
            let arg = arguments;
            if (immediate) {
                if (!timeoutID) result = func.apply(func, arg);
                timeoutID = setTimeout(() => {
                    timeoutID = 0;
                }, wait);
            } else {
                timeoutID = setTimeout(() => {
                    func.apply(func, arg);
                }, wait);
            }
            return result;
        };
        // 取消防抖
        debounced.cancel = function () {
            clearTimeout(timeoutID);
            timeoutID = 0;
        };
        return debounced;
    }

    /**
     * @description: 节流函数
     * @param {Function} func 回调函数
     * @param {Number}  wait 等待时间 ms
     * @param {Object} options leading代表是否开头执行，tailing代表是否结尾执行
     * @return: null
     */
    public static throttle(func: Function, wait: number, options: IThrottleOptions = {leading: true, trailing: true}) {
        let timeoutID: number = 0;
        let previous = 0;

        let throttled: FunDebounceOrThrottle = function () {
            let now = new Date().getTime();
            if (!previous && !options.leading) previous = now;
            let remaining = wait - (now - previous);
            let arg = arguments;
            if (remaining <= 0 || remaining > wait) {
                if (timeoutID) {
                    clearTimeout(timeoutID);
                    timeoutID = 0;
                }
                previous = now;
                func.apply(func, arg);
            } else if (!timeoutID && options.trailing) {
                timeoutID = window.setTimeout(() => {
                    previous = !options.leading ? 0 : new Date().getTime();
                    timeoutID = 0;
                    func.apply(func, arg);
                }, remaining);
            }
        };
        // 取消节流
        throttled.cancel = function () {
            clearTimeout(timeoutID);
            previous = 0;
            timeoutID = 0;
        };
        return throttled;
    }
```

现在，动态添加和取消也有了，装饰器版本也有了，一下子学习这么多，想必一定很累吧，爽歪歪，完美收工！

## 参考

- [防抖和节流原理分析](https://juejin.cn/post/6844903662519599111)
- [再谈函数节流与防抖 - 利用装饰器 @decorator 来实现](https://www.jianshu.com/p/c9fe37cdf200)
- [装饰者模式和TypeScript装饰器](https://segmentfault.com/a/1190000022415199)
- [这篇文章助你理解函数防抖与函数节流](https://mp.weixin.qq.com/s/ArXO9ytrOZ44uaXyzkYKBw)

