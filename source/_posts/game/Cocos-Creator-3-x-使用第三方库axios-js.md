---
title: Cocos Creator 3.x 使用第三方库axios.js
comments: true
categories: game
tags:
  - Cocos
  - axios
abbrlink: 2f1c8930
date: 2021-08-25 14:49:30
---

终于升级到 `Cocos Creator 3.x` 版本了，接下来就是一步步的踩坑，比如之前的项目用的是 [axios](https://github.com/axios/axios) 这个类库，`2.x` 正常引入，3.x就报错了，下面说说这个报错，以及如何解决。
<!--more-->

下面说一下 具体说明

## 介绍

> ### Axios 是什么?
>
> Axios 是一个基于 *[promise](https://javascript.info/promise-basics)* 网络请求库，作用于[`node.js`](https://nodejs.org/) 和浏览器中。 它是 *[isomorphic](https://www.lullabot.com/articles/what-is-an-isomorphic-application)* 的(即同一套代码可以运行在浏览器和node.js中)。在服务端它使用原生 node.js `http` 模块, 而在客户端 (浏览端) 则使用 XMLHttpRequests。

## 环境：

####  [npm](https://www.npmjs.com/)

####  [axios](https://github.com/axios/axios)

####  [axios-retry](https://github.com/softonic/axios-retry)

####  [is-retry-allowed](https://github.com/sindresorhus/is-retry-allowed)

相关链接：[axios官方文档(最新)](https://axios-http.com/zh/docs/instance)   [axios中文文档（原）](http://axios-js.com/zh-cn/docs/)

### API 说明

2021年12月01 更新说明

现在官方网站API 和demo已经说的很清楚了，也很容易理解，直接配置测试即可，这里不再赘述。

## 步骤

首先关于[Cocos Creator 2.4.x](https://www.cocos.com/docs) 版本如何引入第三方库，前面几篇文章反复的说明，大家可以借鉴前面的文章，按部就班基本没有什么问题。现在`3.x` 版本如果 不是按照官方npm 的安装方法，而是采取手动安装的话，会报错。

比如下面代码：

```typescript
import {_decorator, Component} from 'cc';
import axios from "../libs/axios";
const {ccclass, property} = _decorator;
@ccclass('FirstScene')
export class FirstScene extends Component {
    testHandler() {
        console.log("smile----:" + JSON.stringify("8888888888"));
        //https://xxxxx/better.json 这个 修改成自己的地址，我这里测试用
        axios.get('https://xxxxx/better.json').then((res) => {
            console.log("登陆成功啊");
            console.log(JSON.stringify(res));
        }).catch(() => {
            console.error("登陆失败");
        });
    }
}
```

报错如下：

```bash
 无法从 file:///Users/smile/work/temp/ccc3/axiosdemo/assets/src/FirstScene.ts 解析出模块 ../libs/axios，已将其视为外部模块。
 你是否遗漏了扩展名？请注意你不能在模块说明符中省略扩展名。
```

很奇怪的错误，找了很久，最后一无所获，后面又回到官方文档看到这几句话：

```javascript
// foo.js
module.exports = {
    a: 1,
    b: 2,
}

module.exports.c = 3;

// test.mjs

// default 指向 module.exports
import foo from './foo.js'; // 等价于 import { default as foo } from './foo.js'
console.log(JSON.stringify(foo)); // {"a":1,"b":2,"c":3}

// 导入 foo 模块的所有导出
import * as module_foo from './foo.js'
console.log(JSON.stringify(module_foo)); // {"c":3,"default":{"a":1,"b":2,"c":3}}

import { a } from './foo.js'
console.log(a); // Error: a is not defined

// 根据上方第三点，c 有独立导出
import { c } from './foo.js'
console.log(c); // 3

```

后面 对比了一下，就是我的类库 导入的时候 没有写 后缀名 .js 。于是带着猜测 添加上后缀。

```typescript
import {_decorator, Component} from 'cc';
import axiosRetry from "../libs/axios-retry";
import axios from "../libs/axios.js";

const {ccclass, property} = _decorator;
export type Constructor<T = unknown> = new (...args: any[]) => T;

@ccclass('FirstScene')
export class FirstScene extends Component {
    start() {
        // Platform.initAPI();
    }

    testHandler() {
        console.log("smile----:" + JSON.stringify("8888888888"));
        axiosRetry(axios, {retries: 3});
        axios.get('https://cdn.mini.52muyou.com/xiaoyouxi/test/game/better.json').then((res) => {
            console.log("登陆成功啊");
            console.log(JSON.stringify(res));
        }).catch(() => {
            console.error("登陆失败");
        });
    }

    testObject() {

    }

    public getMyComponent<T extends Component>(classConstructor: Constructor<T>): T {
        return this.node.getComponent(classConstructor);
    }
}

```

发现编辑器不再报错，成功获取到数据

```
smile----:"8888888888"
登陆成功啊
{"data":...}
```

随后添加 一个 插件 axios-retry   这个插件一直没有成功，后面我就直接 把代码修改为ts 文件引入了。最终代码如下：

```typescript
const namespace = 'axios-retry';
/**
 * @param  {Error}  error
 * @return {boolean}
 */
export function isNetworkError(error) {
    return (
        !error.response &&
        Boolean(error.code) && // Prevents retrying cancelled requests
        error.code !== 'ECONNABORTED' && // Prevents retrying timed out requests
        isRetryAllowed(error)
    ); // Prevents retrying unsafe errors
}

const SAFE_HTTP_METHODS = ['get', 'head', 'options'];
const IDEMPOTENT_HTTP_METHODS = SAFE_HTTP_METHODS.concat(['put', 'delete']);

/**
 * @param  {Error}  error
 * @return {boolean}
 */
export function isRetryableError(error) {
    return (
        error.code !== 'ECONNABORTED' &&
        (!error.response || (error.response.status >= 500 && error.response.status <= 599))
    );
}

/**
 * @param  {Error}  error
 * @return {boolean}
 */
export function isSafeRequestError(error) {
    if (!error.config) {
        // Cannot determine if the request can be retried
        return false;
    }

    return isRetryableError(error) && SAFE_HTTP_METHODS.indexOf(error.config.method) !== -1;
}

/**
 * @param  {Error}  error
 * @return {boolean}
 */
export function isIdempotentRequestError(error) {
    if (!error.config) {
        // Cannot determine if the request can be retried
        return false;
    }

    return isRetryableError(error) && IDEMPOTENT_HTTP_METHODS.indexOf(error.config.method) !== -1;
}

/**
 * @param  {Error}  error
 * @return {boolean}
 */
export function isNetworkOrIdempotentRequestError(error) {
    return isNetworkError(error) || isIdempotentRequestError(error);
}

/**
 * @return {number} - delay in milliseconds, always 0
 */
function noDelay() {
    return 0;
}

/**
 * @param  {number} [retryNumber=0]
 * @return {number} - delay in milliseconds
 */
export function exponentialDelay(retryNumber = 0) {
    const delay = Math.pow(2, retryNumber) * 100;
    const randomSum = delay * 0.2 * Math.random(); // 0-20% of the delay
    return delay + randomSum;
}

/**
 * Initializes and returns the retry state for the given request/config
 * @param  {AxiosRequestConfig} config
 * @return {Object}
 */
function getCurrentState(config) {
    const currentState = config[namespace] || {};
    currentState.retryCount = currentState.retryCount || 0;
    config[namespace] = currentState;
    return currentState;
}

/**
 * Returns the axios-retry options for the current request
 * @param  {AxiosRequestConfig} config
 * @param  {AxiosRetryConfig} defaultOptions
 * @return {AxiosRetryConfig}
 */
function getRequestOptions(config, defaultOptions) {
    return Object.assign({}, defaultOptions, config[namespace]);
}

/**
 * @param  {Axios} axios
 * @param  {AxiosRequestConfig} config
 */
function fixConfig(axios, config) {
    if (axios.defaults.agent === config.agent) {
        delete config.agent;
    }
    if (axios.defaults.httpAgent === config.httpAgent) {
        delete config.httpAgent;
    }
    if (axios.defaults.httpsAgent === config.httpsAgent) {
        delete config.httpsAgent;
    }
}

/**
 * Adds response interceptors to an axios instance to retry requests failed due to network issues
 *
 * @example
 *
 * import axios from 'axios';
 *
 * axiosRetry(axios, { retries: 3 });
 *
 * axios.get('http://example.com/test') // The first request fails and the second returns 'ok'
 *   .then(result => {
 *     result.data; // 'ok'
 *   });
 *
 * // Exponential back-off retry delay between requests
 * axiosRetry(axios, { retryDelay : axiosRetry.exponentialDelay});
 *
 * // Custom retry delay
 * axiosRetry(axios, { retryDelay : (retryCount) => {
 *   return retryCount * 1000;
 * }});
 *
 * // Also works with custom axios instances
 * const client = axios.create({ baseURL: 'http://example.com' });
 * axiosRetry(client, { retries: 3 });
 *
 * client.get('/test') // The first request fails and the second returns 'ok'
 *   .then(result => {
 *     result.data; // 'ok'
 *   });
 *
 * // Allows request-specific configuration
 * client
 *   .get('/test', {
 *     'axios-retry': {
 *       retries: 0
 *     }
 *   })
 *   .catch(error => { // The first request fails
 *     error !== undefined
 *   });
 *
 * @param {Axios} axios An axios instance (the axios object or one created from axios.create)
 * @param {Object} [defaultOptions]
 * @param {number} [defaultOptions.retries=3] Number of retries
 * @param {boolean} [defaultOptions.shouldResetTimeout=false]
 *        Defines if the timeout should be reset between retries
 * @param {Function} [defaultOptions.retryCondition=isNetworkOrIdempotentRequestError]
 *        A function to determine if the error can be retried
 * @param {Function} [defaultOptions.retryDelay=noDelay]
 *        A function to determine the delay between retry requests
 */
export default function axiosRetry(axios, defaultOptions) {
    axios.interceptors.request.use(config => {
        const currentState = getCurrentState(config);
        currentState.lastRequestTime = Date.now();
        return config;
    });

    axios.interceptors.response.use(null, error => {
        const config = error.config;

        // If we have no information to retry the request
        if (!config) {
            return Promise.reject(error);
        }

        const {
            retries = 3,
            retryCondition = isNetworkOrIdempotentRequestError,
            retryDelay = noDelay,
            shouldResetTimeout = false
        } = getRequestOptions(config, defaultOptions);

        const currentState = getCurrentState(config);

        const shouldRetry = retryCondition(error) && currentState.retryCount < retries;

        if (shouldRetry) {
            currentState.retryCount += 1;
            const delay = retryDelay(currentState.retryCount, error);

            // Axios fails merging this configuration to the default configuration because it has an issue
            // with circular structures: https://github.com/mzabriskie/axios/issues/370
            fixConfig(axios, config);

            if (!shouldResetTimeout && config.timeout && currentState.lastRequestTime) {
                const lastRequestDuration = Date.now() - currentState.lastRequestTime;
                // Minimum 1ms timeout (passing 0 or less to XHR means no timeout)
                config.timeout = Math.max(config.timeout - lastRequestDuration - delay, 1);
            }

            config.transformRequest = [data => data];

            return new Promise(resolve => setTimeout(() => resolve(axios(config)), delay));
        }

        return Promise.reject(error);
    });
}
const denyList = new Set([
    'ENOTFOUND',
    'ENETUNREACH',

    // SSL errors from https://github.com/nodejs/node/blob/fc8e3e2cdc521978351de257030db0076d79e0ab/src/crypto/crypto_common.cc#L301-L328
    'UNABLE_TO_GET_ISSUER_CERT',
    'UNABLE_TO_GET_CRL',
    'UNABLE_TO_DECRYPT_CERT_SIGNATURE',
    'UNABLE_TO_DECRYPT_CRL_SIGNATURE',
    'UNABLE_TO_DECODE_ISSUER_PUBLIC_KEY',
    'CERT_SIGNATURE_FAILURE',
    'CRL_SIGNATURE_FAILURE',
    'CERT_NOT_YET_VALID',
    'CERT_HAS_EXPIRED',
    'CRL_NOT_YET_VALID',
    'CRL_HAS_EXPIRED',
    'ERROR_IN_CERT_NOT_BEFORE_FIELD',
    'ERROR_IN_CERT_NOT_AFTER_FIELD',
    'ERROR_IN_CRL_LAST_UPDATE_FIELD',
    'ERROR_IN_CRL_NEXT_UPDATE_FIELD',
    'OUT_OF_MEM',
    'DEPTH_ZERO_SELF_SIGNED_CERT',
    'SELF_SIGNED_CERT_IN_CHAIN',
    'UNABLE_TO_GET_ISSUER_CERT_LOCALLY',
    'UNABLE_TO_VERIFY_LEAF_SIGNATURE',
    'CERT_CHAIN_TOO_LONG',
    'CERT_REVOKED',
    'INVALID_CA',
    'PATH_LENGTH_EXCEEDED',
    'INVALID_PURPOSE',
    'CERT_UNTRUSTED',
    'CERT_REJECTED',
    'HOSTNAME_MISMATCH'
]);

// TODO: Use `error?.code` when targeting Node.js 14
export function isRetryAllowed(error) {
    return !denyList.has(error && error.code);
}
// Compatibility with CommonJS
axiosRetry.isNetworkError = isNetworkError;
axiosRetry.isSafeRequestError = isSafeRequestError;
axiosRetry.isIdempotentRequestError = isIdempotentRequestError;
axiosRetry.isNetworkOrIdempotentRequestError = isNetworkOrIdempotentRequestError;
axiosRetry.exponentialDelay = exponentialDelay;
axiosRetry.isRetryableError = isRetryableError;

```

最后完整调用测试：

```typescript
import {_decorator, Component} from 'cc';
import axiosRetry from "../libs/axios-retry";
import axios from "../libs/axios.js";

const {ccclass, property} = _decorator;
export type Constructor<T = unknown> = new (...args: any[]) => T;

@ccclass('FirstScene')
export class FirstScene extends Component {
    testHandler() {
        console.log("smile----:" + JSON.stringify("8888888888"));
        axiosRetry(axios, {retries: 3});
        axios.get('https://xxxxx/better.json').then((res) => {
            console.log("登陆成功啊");
            console.log(JSON.stringify(res));
        }).catch(() => {
            console.error("登陆失败");
        });
    }
}
```

我们可以修改一下 地址 然后回发现 重试次数 已经生效 三次。

![image-20210825150811399](Cocos-Creator-3-x-使用第三方库axios-js/image-20210825150811399.png)

### 其他

**typescript忽略文件或者内容** 参考地址 [TS语法忽略、eslint忽略](https://blog.csdn.net/m0_38069630/article/details/108194188)

```typescript
单行忽略
// @ts-ignore

忽略全文
// @ts-nocheck

取消忽略全文
// @ts-check

```

**eslint忽略+typescript忽略**

```typescript
/* eslint-disable */
const watermark = require("watermark-dom"); // @ts-ignore
/* eslint-enable */

```

`Object.values` 提示不能使用

![image-20210825151158103](Cocos-Creator-3-x-使用第三方库axios-js/image-20210825151158103.png)

修改 `tsconfig.json`的`compilerOptions` 选项

```json
{
  "compilerOptions": {
    "strict": false,
    "lib": [
      "es2017", "dom"
    ]
  }
}
```

更新细节参考：[编译选项](https://www.tslang.cn/docs/handbook/compiler-options.html)

最后  给个demo地址 [点击前往](https://github.com/jsroads/mylibs/tree/main/axios/3.x/axiosdemo)

## 其他相关

- [qs](https://github.com/ljharb/qs)  一个 url参数转化 (parse和stringify)的轻量级js库
- **fly.js** 一个基于promise的http请求库, 可以用在node.js, Weex, 微信小程序, 浏览器, React Native中

