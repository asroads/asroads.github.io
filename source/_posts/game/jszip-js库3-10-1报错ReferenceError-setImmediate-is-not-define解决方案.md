---
title: 'jszip.js库3.10.1报错ReferenceError: setImmediate is not define解决方案'
comments: true
categories: game
tags:
  - 小游戏
  - jszip
  - Cocos
abbrlink: 657141b7
date: 2024-10-29 12:48:38
---

之前为了小游戏加载配置表使用了[jszip](https://github.com/Stuk/jszip)这个库，前段时间，升级了最新版本[3.10.1版本](https://github.com/Stuk/jszip/releases/tag/v3.10.1)在微信小游戏和抖音小游戏上都表现正常，到了QQ小游戏上之后就出现了报错，下面就简单的记录一下这个错误的原因以及解决方法。
<!--more-->

## 环境

- Mac
- Cocos Creator 2.4.13
- QQ小游戏

## 相关文档

- [Cocos Creator](https://docs.cocos.com/creator/2.4/manual/zh/) 
- [QQ小游戏](https://q.qq.com/wiki/develop/game/API/)
- [jszip](https://github.com/Stuk/jszip)
- [MDN文档](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/setImmediate)

## 如何使用

1. 首先去官方地址 下载我们的版本的文件 下载地址：[点击进入3.10.1](https://github.com/Stuk/jszip/releases/tag/v3.10.1)
2. 下载后 解压我们的文件：结构如下：
   ![image-20241029132711946](./jszip-js库3-10-1报错ReferenceError-setImmediate-is-not-define解决方案/image-20241029132711946.png)



3. 打开文件 找到 jszip.js 文件

![image-20241029132745245](./jszip-js库3-10-1报错ReferenceError-setImmediate-is-not-define解决方案/image-20241029132745245.png)

4. 在代码的首部修改如下：

原来代码 第一行

```
(function(f){if(typeof exports==="object"&&typeof module!=="undefined"){module.exports=f()}else if(typeof define==="function"&&define.amd){define([],f)}else{var g;if(typeof window!=="undefined"){g=window}else if(typeof global!=="undefined"){g=global}else if(typeof self!=="undefined"){g=self}else{g=this}g.JSZip = f()}})(function(){var define,module,exports;return (function e(t,n,r){function s(o,u){if(!n[o]){if(!t[o]){var a=typeof require=="function"&&require;if(!u&&a)return a(o,!0);if(i)return i(o,!0);var f=new Error("Cannot find module '"+o+"'");throw f.code="MODULE_NOT_FOUND",f}var l=n[o]={exports:{}};t[o][0].call(l.exports,function(e){var n=t[o][1][e];return s(n?n:e)},l,l.exports,e,t,n,r)}return n[o].exports}var i=typeof require=="function"&&require;for(var o=0;o<r.length;o++)s(r[o]);return s})({1:[function(require,module,exports){
```

修改后

```
(function(f){if(typeof exports==="object"&&typeof module!=="undefined"){module.exports=f(),window.JSZip = window.JSZip||module.exports;}else if(typeof define==="function"&&define.amd){define([],f)}else{var g;if(typeof window!=="undefined"){g=window}else if(typeof global!=="undefined"){g=global}else if(typeof self!=="undefined"){g=self}else{g=this}g.JSZip = f()}})(function(){var define,module,exports;return (function e(t,n,r){function s(o,u){if(!n[o]){if(!t[o]){var a=typeof require=="function"&&require;if(!u&&a)return a(o,!0);if(i)return i(o,!0);var f=new Error("Cannot find module '"+o+"'");throw f.code="MODULE_NOT_FOUND",f}var l=n[o]={exports:{}};t[o][0].call(l.exports,function(e){var n=t[o][1][e];return s(n?n:e)},l,l.exports,e,t,n,r)}return n[o].exports}var i=typeof require=="function"&&require;for(var o=0;o<r.length;o++)s(r[o]);return s})({1:[function(require,module,exports){
        
```

备注：核心修改逻辑

```
{module.exports=f()}====>{module.exports=f(),window.JSZip = window.JSZip||module.exports;}
```

如果我们的项目是Typescript项目 记得同时添加 声明文件（.d.ts）

![image-20241029133158408](./jszip-js库3-10-1报错ReferenceError-setImmediate-is-not-define解决方案/image-20241029133158408.png)

此时，已经可以在web环境 和微信小游戏以及抖音小游戏正常使用了。下面是QQ小游戏平台报错的现象以及修改方法。

## 报错现象

### 报错信息

```bash
Uncaught (in promise) ReferenceError: setImmediate is not defined
    at Object.exports.delay (index.js? [sm]:5876)
    at StreamHelper.resume (index.js? [sm]:5470)
    at index.js? [sm]:5430
    at new Promise (<anonymous>)
    at _accumulate (index.js? [sm]:5413)
    at StreamHelper.accumulate (index.js? [sm]:5458)
    at ZipObject.async (index.js? [sm]:6241)
    at index.js? [sm]:3536
    at Array.forEach (<anonymous>)
    at Helloworld.loadMiniGameZipConfig (index.js? [sm]:3535)
```

![image-20241029105706239](./jszip-js库3-10-1报错ReferenceError-setImmediate-is-not-define解决方案/image-20241029105706239.png)

### 报错代码

```javascript
/**
 * Defer the call of a function.
 * @param {Function} callback the function to call asynchronously.
 * @param {Array} args the arguments to give to the callback.
 */
exports.delay = function(callback, args, self) {
    setImmediate(function () {
        callback.apply(self || null, args || []);
    });
};
```

### 报错分析

我们同事在官方库的 Issues 内看到了连个讨论

帖子一：generateAsync not work and no error thrown on v3.10.0 and v3.10.1[#864](https://github.com/Stuk/jszip/issues/864)

帖子二: JSZip v3.10+ breaks in some sandboxed browser environment because dependency setimmediate breaks [#909](https://github.com/Stuk/jszip/issues/909)

通过帖子我们可以看到 这个问题 只在最新的版本 3.10.1版本才有 ，于是我对比了 前一个版本后到如下结论：

3.10.1 使用了一个库：[setImmediate.js](https://github.com/YuzuJS/setImmediate)

```javascript
(function (global, undefined) {
    "use strict";

    if (global.setImmediate) {
        return;
    }

    var nextHandle = 1; // Spec says greater than zero
    var tasksByHandle = {};
    var currentlyRunningATask = false;
    var doc = global.document;
    var registerImmediate;

    function setImmediate(callback) {
      // Callback can either be a function or a string
      if (typeof callback !== "function") {
        callback = new Function("" + callback);
      }
      // Copy function arguments
      var args = new Array(arguments.length - 1);
      for (var i = 0; i < args.length; i++) {
          args[i] = arguments[i + 1];
      }
      // Store and register the task
      var task = { callback: callback, args: args };
      tasksByHandle[nextHandle] = task;
      registerImmediate(nextHandle);
      return nextHandle++;
    }

    function clearImmediate(handle) {
        delete tasksByHandle[handle];
    }

    function run(task) {
        var callback = task.callback;
        var args = task.args;
        switch (args.length) {
        case 0:
            callback();
            break;
        case 1:
            callback(args[0]);
            break;
        case 2:
            callback(args[0], args[1]);
            break;
        case 3:
            callback(args[0], args[1], args[2]);
            break;
        default:
            callback.apply(undefined, args);
            break;
        }
    }

    function runIfPresent(handle) {
        // From the spec: "Wait until any invocations of this algorithm started before this one have completed."
        // So if we're currently running a task, we'll need to delay this invocation.
        if (currentlyRunningATask) {
            // Delay by doing a setTimeout. setImmediate was tried instead, but in Firefox 7 it generated a
            // "too much recursion" error.
            setTimeout(runIfPresent, 0, handle);
        } else {
            var task = tasksByHandle[handle];
            if (task) {
                currentlyRunningATask = true;
                try {
                    run(task);
                } finally {
                    clearImmediate(handle);
                    currentlyRunningATask = false;
                }
            }
        }
    }

    function installNextTickImplementation() {
        registerImmediate = function(handle) {
            process.nextTick(function () { runIfPresent(handle); });
        };
    }

    function canUsePostMessage() {
        // The test against `importScripts` prevents this implementation from being installed inside a web worker,
        // where `global.postMessage` means something completely different and can't be used for this purpose.
        if (global.postMessage && !global.importScripts) {
            var postMessageIsAsynchronous = true;
            var oldOnMessage = global.onmessage;
            global.onmessage = function() {
                postMessageIsAsynchronous = false;
            };
            global.postMessage("", "*");
            global.onmessage = oldOnMessage;
            return postMessageIsAsynchronous;
        }
    }

    function installPostMessageImplementation() {
        // Installs an event handler on `global` for the `message` event: see
        // * https://developer.mozilla.org/en/DOM/window.postMessage
        // * http://www.whatwg.org/specs/web-apps/current-work/multipage/comms.html#crossDocumentMessages

        var messagePrefix = "setImmediate$" + Math.random() + "$";
        var onGlobalMessage = function(event) {
            if (event.source === global &&
                typeof event.data === "string" &&
                event.data.indexOf(messagePrefix) === 0) {
                runIfPresent(+event.data.slice(messagePrefix.length));
            }
        };

        if (global.addEventListener) {
            global.addEventListener("message", onGlobalMessage, false);
        } else {
            global.attachEvent("onmessage", onGlobalMessage);
        }

        registerImmediate = function(handle) {
            global.postMessage(messagePrefix + handle, "*");
        };
    }

    function installMessageChannelImplementation() {
        var channel = new MessageChannel();
        channel.port1.onmessage = function(event) {
            var handle = event.data;
            runIfPresent(handle);
        };

        registerImmediate = function(handle) {
            channel.port2.postMessage(handle);
        };
    }

    function installReadyStateChangeImplementation() {
        var html = doc.documentElement;
        registerImmediate = function(handle) {
            // Create a <script> element; its readystatechange event will be fired asynchronously once it is inserted
            // into the document. Do so, thus queuing up the task. Remember to clean up once it's been called.
            var script = doc.createElement("script");
            script.onreadystatechange = function () {
                runIfPresent(handle);
                script.onreadystatechange = null;
                html.removeChild(script);
                script = null;
            };
            html.appendChild(script);
        };
    }

    function installSetTimeoutImplementation() {
        registerImmediate = function(handle) {
            setTimeout(runIfPresent, 0, handle);
        };
    }

    // If supported, we should attach to the prototype of global, since that is where setTimeout et al. live.
    var attachTo = Object.getPrototypeOf && Object.getPrototypeOf(global);
    attachTo = attachTo && attachTo.setTimeout ? attachTo : global;

    // Don't get fooled by e.g. browserify environments.
    if ({}.toString.call(global.process) === "[object process]") {
        // For Node.js before 0.9
        installNextTickImplementation();

    } else if (canUsePostMessage()) {
        // For non-IE10 modern browsers
        installPostMessageImplementation();

    } else if (global.MessageChannel) {
        // For web workers, where supported
        installMessageChannelImplementation();

    } else if (doc && "onreadystatechange" in doc.createElement("script")) {
        // For IE 6–8
        installReadyStateChangeImplementation();

    } else {
        // For older browsers
        installSetTimeoutImplementation();
    }

    attachTo.setImmediate = setImmediate;
    attachTo.clearImmediate = clearImmediate;
}(typeof self === "undefined" ? typeof global === "undefined" ? this : global : self));
```

3.9.1 使用的是另外一个库：[set-immediate-shim](https://github.com/sindresorhus/set-immediate-shim)

```javascript
const setImmediate = typeof globalThis.setImmediate === 'function' ? globalThis.setImmediate : (...arguments_) => {
	arguments_.splice(1, 0, 0);
	setTimeout(...arguments_);
};

export default setImmediate;
```

两者的核心代码是有差异，然后又去查阅了其他资料 于是有下面两个方案解决：

### 解决方法

#### 方案一:3.10.1版本使用 setTimeout(fn, 0)替代

```javascript
/**
 * Defer the call of a function.
 * @param {Function} callback the function to call asynchronously.
 * @param {Array} args the arguments to give to the callback.
 */
exports.delay = function(callback, args, self) {
   setTimeout(function () {
      callback.apply(self || null, args || []);
    },0);
};
```

#### 方案二：降级使用3.9.1版本

下载地址：https://github.com/Stuk/jszip/releases/tag/v3.9.1

## 参考

- [postMessage is 'undefined' and throws 'setImmediate is not defined' error #68](https://github.com/YuzuJS/setImmediate/issues/68)
- [Tampermonkey breaks docx https://github.com/dolanmiu/docx #1600](https://github.com/Tampermonkey/tampermonkey/issues/1600)
- [异常: setImmediate is not defined](https://blog.csdn.net/rencaishigepi/article/details/111900145)

