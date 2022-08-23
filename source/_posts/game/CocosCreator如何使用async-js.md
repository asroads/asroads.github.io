---
title: CocosCreator如何使用async.js
comments: true
categories: game
tags:
  - Cocos
  - async.js
abbrlink: 61e73eb2
date: 2022-08-23 12:05:04
---

有时候为了提高用户体验，需要分帧加载，[async.js](https://github.com/caolan/async) 是个优秀的第三方库，最近在看一些关于分帧加载的文章的时候，作者推荐了这个第三方库，于是想拿来用一下效果，于是乎，导入官方`dist`目录下的文件直接报错，于是乎自己用[rollup.js](https://rollupjs.org/guide/zh/)重新对源码编译生成一份儿可以用的，下面就说说如何构建。
<!--more-->

## 背景

官方最新版本的[async.js](https://github.com/caolan/async)无法使用，低版本貌似可以，但是秉承着学习和向未来编程的方法，就自己编译一下，其实把散碎的js直接导入也是可以的，但是毕竟一个`js`文件看起来更加简洁和优雅。

首先我们看一下，直接使用官方生成的报错如下：
![image-20220823123023551](CocosCreator%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8async-js/image-20220823123023551.png)

```
load script [./assets/Script/libs/async] failed : ReferenceError: regeneratorRuntime is not defined
```

论坛里有人和我报错相同，但是下面没人给出解决方法。于是，想着自己突破一下吧！

## 环境

- Cocos Creator 2.3.3
- Windows10
- Webstorm 编辑器（你用VSCode一样）
- nodejs 环境

## 解决方案

### 方案一：在原构建成功的文件里添加 runtime.js

1. 首先找到项目地址：https://github.com/caolan/async/tree/master/dist

![image-20220823201728363](CocosCreator%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8async-js/image-20220823201728363.png)

2. 复制 [async.js](https://github.com/caolan/async/blob/master/dist/async.js) 到我们的项目 新建一个`libs`文件夹

3. 如果是ts 项目记得 找到对应的声明文件 同样复制到`libs`目录下

   下载地址: https://github.com/DefinitelyTyped/DefinitelyTyped/tree/master/types/async

4. 根据报错得知错误原因是 使用了 迭代器语法 编译的时候报错

```javascript
class DLL {
        constructor() {
            this.head = this.tail = null;
            this.length = 0;
        }

        //...此处省略无用代码

        toArray() {
            return [...this]
        }

        *[Symbol.iterator] () {
            var cur = this.head;
            while (cur) {
                yield cur.data;
                cur = cur.next;
            }
        }

        remove (testFn) {
            var curr = this.head;
            while(curr) {
                var {next} = curr;
                if (testFn(curr)) {
                    this.removeLink(curr);
                }
                curr = next;
            }
            return this;
        }
    }
```

编译后 变成了如下：
![image-20220823202601590](CocosCreator%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8async-js/image-20220823202601590.png)

因为缺少了全局变量 regeneratorRuntime 导致报错，这里是报错的主要原因。

5. 如何把缺少的 regeneratorRuntime 变量补上去呢，这里给出一个：

下载地址：https://github.com/facebook/regenerator/blob/main/packages/runtime/runtime.js

6. 直接复制 [runtime.js](https://github.com/facebook/regenerator/blob/main/packages/runtime/runtime.js) 内容到 async.js 的最上方即可，当然也可以新建一个runtime.js文件 然后以插件形式导入项目。
7. 重新启动 Cocos Creator 项目，写测试代码：HelloWorld.ts

```typescript
import {eachLimit} from "./libs/async";
const {ccclass, property} = cc._decorator;
@ccclass
export default class Helloworld extends cc.Component {

    @property(cc.Label)
    label: cc.Label = null;

    @property
    text: string = 'hello';
    @property
    count: number = 0;
    start () {
        // init logic
        this.label.string = this.text;
    }
    touchHandler (event: cc.Event.EventTouch,data) {
        console.log("$$:",Date.now(),"  ",this.count++);
        let array = []
        for (let i = 0; i < 123; i++) {
            array.push(i)
        }
        // async.eachLimit(array,  3,  (index,cb)  =>  {
        //     console.log("smile----index:" + JSON.stringify(index));
        //     this.scheduleOnce(()=>{
        //         let res = index>99?1:null
        //         if(res===null){
        //             cb&&cb();
        //         }
        //     }, 1);
        //     // this.scheduleOnce(cb,  1);
        // });
        eachLimit(array,  3,  (index,cb)  =>  {
            console.log("smile----index:" + JSON.stringify(index));
            this.scheduleOnce(()=>{
                let res = index>99?1:null
                if(res===null){
                    cb&&cb();
                }
            }, 0.5);
            // this.scheduleOnce(cb,  1);
        });
    }
}

```

8. 运行查看效果

![GIF100](CocosCreator%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8async-js/GIF100.gif)

###  自定义构建项目

1. 使用WebStorm新建一个项目，结构如下

![image-20220823122041396](CocosCreator%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8async-js/image-20220823122041396.png)



2. 下面逐一说一下每个文件的内容 package.json

```json
{
  "name": "async-build-demo",
  "version": "1.0.0",
  "description": "async-build-demo",
  "main": "./dist/index.js",
  "scripts": {
    "build": "rollup --config"
  },
  "files": [
    "dist"
  ],
  "type": "module",
  "module": "ES6",
  "author": "jsroads",
  "repository": {
    "type": "git",
    "url": "https://github.com"
  },
  "license": "ISC",
  "devDependencies": {
    "@types/node": "^16.3.3",
    "rollup": "^2.53.2"
  }
}

```

3. rollup.config.js 内容如下

```javascript
/**
 * Created by jsroads on 2022/8/20 12:17
 * Note:
 */
export default {
    input: './src/index.js', //输入 打包的文件
    output: [{  //输出 编译后文件
        file: './dist/async-cjs.js', //文件名
        format: 'cjs', //打包规范
        exports: 'named',
        banner: '// jsroads libs',  //头注释
        footer: '// powered by jsroads' //尾注释
    }, {
        file: './dist/async-es.js',
        format: 'esm',
        exports: 'named',
        banner: '// jsroads libs',  //头注释
        footer: '// powered by jsroads' //尾注释
    }]
}
```

4. src 目录下文件是 从 https://github.com/caolan/async/tree/master/lib 文件下的所有文件 复制过来的。
5. 首先运行 npm install  加载项目所需的依赖。
6. 然后运行 命令 rollup -c

![image-20220823122411822](CocosCreator%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8async-js/image-20220823122411822.png)

7. 此时如下图所示 编译后的文件已经生成。

![image-20220823122430329](CocosCreator%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8async-js/image-20220823122430329.png)

8. 因项目是 typescript项目 所以需要下载对应的声明文件。文件地址 https://github.com/DefinitelyTyped/DefinitelyTyped/tree/master/types/async

![image-20220823122720413](CocosCreator%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8async-js/image-20220823122720413.png)

9. 然后把 index.d.ts 重命名为 async-es.d.ts 和 dist 文件夹下生成的 async-es.js 一起放到 Cocos Creator项目的libs 下（记得删除原来报错文件）,重启项目。
9. 继续添加 我们的  [runtime.js](https://github.com/facebook/regenerator/blob/main/packages/runtime/runtime.js)文件 https://github.com/facebook/regenerator/blob/main/packages/runtime/runtime.js

11. 重复方案一的其他步骤。

**注意：如果有微信小游戏环境报错的，可以 在 [runtime.js](https://github.com/facebook/regenerator/blob/main/packages/runtime/runtime.js) 的末尾添加：目前（2022.08.23）官方代码已经添加了这些。**

```javascript
try {
  window.regeneratorRuntime = runtime;
} catch (accidentalStrictMode) {
  ···
  Function("r", "regeneratorRuntime = r")(runtime);
}
```





最后 测试项目地址：[点击前往](https://github.com/jsroads/mylibs/tree/main/async.js)

## 总结

总的来讲，此问题主要是添加一个 runtime.js 去全局即可，剩余的是自定义构建这个类库，比如我只用到了分帧加载，那么我就只构建自己用到的方法和文件，压缩后只有4KB左右。

## 参考

- [使用 RAIL 模型衡量性能](https://web.dev/rail/)

- [微信小游戏开发之Cocos Creator场景切换巨慢的原因分析和耗时操作异步执行的解决方案](https://zhuanlan.zhihu.com/p/346640391)
- [前端打包工具UglifyJS使用入门](https://mp.weixin.qq.com/s/5bCkXWNY86SEjBgqOjqMpQ)
- [async.js在Cocos Creator中的应用](https://mp.weixin.qq.com/s/4nGOaaeHclAu6CUDGbJ3LQ)
- [cocoscreator中实现分帧加载的一种方式](https://mp.weixin.qq.com/s/nlruIs70GSXzIBzYxfnUlQ)
