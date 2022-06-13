---
title: 使用Rolup.js构建一个自己的类库
comments: true
categories: tool
tags:
  - rollupjs
  - ts
  - js
abbrlink: 1c7e3cb2
date: 2022-06-12 11:43:41
---

代码编程，程序开发久了，基本都会逐渐形成一套自己收集的技术类库和常用的一些方法，有时候是从优秀的大神那里学习，有的是通过自己工作的摸索，然后都想作为类库为自己所用，有个源码更新的地方，甚至想到后期拿到社区开源供大家使用。
<!--more-->
下面说一下如何使用`Rollup.js`编译自己的类库，然后供`Cocos Creator`使用。

## 环境

- Windows10
- npm
- ts
- rollup.js
- Cocos Creator2.3.3

## 新建项目

1. 首先我们新建一个项目名字叫做`tslibsdemo`
2. 新建一个 `package.json` 内容如下：

```json
{
  "name": "tslibsdemo",
  "version": "1.0.0",
  "description": "tslibsdemo",
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
    "@rollup/plugin-typescript": "^8.2.3",
    "@types/node": "^16.3.3",
    "rollup": "^2.53.2",
    "ts-node": "^10.1.0",
    "tslib": "^2.3.0",
    "typescript": "^4.3.5"
  }
}
```

3. 新建一个 `rollup.config.js` 文件，内容如下:

```javascript
import typescript from '@rollup/plugin-typescript';

export default {
    input: 'src/index.ts',
    output: {
        dir: './dist',
        format: 'esm',
        name: 'MyBundle'
    },
    plugins: [typescript()]
};
```

4. 新建 `tsconfig.json` 文件 内容如下：

```json
{
  "compilerOptions": {
    /* Visit https://aka.ms/tsconfig.json to read more about this file */

    /* Basic Options */
    // "incremental": true,                         /* Enable incremental compilation */
    "target": "ES6",                                /* Specify ECMAScript target version: 'ES3' (default), 'ES5', 'ES2015', 'ES2016', 'ES2017', 'ES2018', 'ES2019', 'ES2020', 'ES2021', or 'ESNEXT'. */
    "module": "ES6",                           /* Specify module code generation: 'none', 'commonjs', 'amd', 'system', 'umd', 'es2015', 'es2020', or 'ESNext'. */
    // "lib": [],                                   /* Specify library files to be included in the compilation. */
    // "allowJs": true,                             /* Allow javascript files to be compiled. */
    // "checkJs": true,                             /* Report errors in .js files. */
    // "jsx": "preserve",                           /* Specify JSX code generation: 'preserve', 'react-native', 'react', 'react-jsx' or 'react-jsxdev'. */
     "declaration": true,                         /* Generates corresponding '.d.ts' file. */
    // "declarationMap": true,                      /* Generates a sourcemap for each corresponding '.d.ts' file. */
    // "sourceMap": true,                           /* Generates corresponding '.map' file. */
    // "outFile": "./",                             /* Concatenate and emit output to single file. */
     "outDir": "./dist",                              /* Redirect output structure to the directory. */
//     "rootDir": "./src",                             /* Specify the root directory of input files. Use to control the output directory structure with --outDir. */
    // "composite": true,                           /* Enable project compilation */
    // "tsBuildInfoFile": "./",                     /* Specify file to store incremental compilation information */
    // "removeComments": true,                      /* Do not emit comments to output. */
    // "noEmit": true,                              /* Do not emit outputs. */
    // "importHelpers": true,                       /* Import emit helpers from 'tslib'. */
    // "downlevelIteration": true,                  /* Provide full support for iterables in 'for-of', spread, and destructuring when targeting 'ES5' or 'ES3'. */
    // "isolatedModules": true,                     /* Transpile each file as a separate module (similar to 'ts.transpileModule'). */

    /* Strict Type-Checking Options */
    "strict": true,                                 /* Enable all strict type-checking options. */
    // "noImplicitAny": true,                       /* Raise error on expressions and declarations with an implied 'any' type. */
    // "strictNullChecks": true,                    /* Enable strict null checks. */
    // "strictFunctionTypes": true,                 /* Enable strict checking of function types. */
    // "strictBindCallApply": true,                 /* Enable strict 'bind', 'call', and 'apply' methods on functions. */
    // "strictPropertyInitialization": true,        /* Enable strict checking of property initialization in classes. */
    // "noImplicitThis": true,                      /* Raise error on 'this' expressions with an implied 'any' type. */
    // "alwaysStrict": true,                        /* Parse in strict mode and emit "use strict" for each source file. */

    /* Additional Checks */
    // "noUnusedLocals": true,                      /* Report errors on unused locals. */
    // "noUnusedParameters": true,                  /* Report errors on unused parameters. */
    // "noImplicitReturns": true,                   /* Report error when not all code paths in function return a value. */
    // "noFallthroughCasesInSwitch": true,          /* Report errors for fallthrough cases in switch statement. */
    // "noUncheckedIndexedAccess": true,            /* Include 'undefined' in index signature results */
    // "noImplicitOverride": true,                  /* Ensure overriding members in derived classes are marked with an 'override' modifier. */
    // "noPropertyAccessFromIndexSignature": true,  /* Require undeclared properties from index signatures to use element accesses. */

    /* Module Resolution Options */
    // "moduleResolution": "node",                  /* Specify module resolution strategy: 'node' (Node.js) or 'classic' (TypeScript pre-1.6). */
    // "baseUrl": "./",                             /* Base directory to resolve non-absolute module names. */
    // "paths": {},                                 /* A series of entries which re-map imports to lookup locations relative to the 'baseUrl'. */
    // "rootDirs": [],                              /* List of root folders whose combined content represents the structure of the project at runtime. */
    // "typeRoots": [],                             /* List of folders to include type definitions from. */
    // "types": [],                                 /* Type declaration files to be included in compilation. */
    // "allowSyntheticDefaultImports": true,        /* Allow default imports from modules with no default export. This does not affect code emit, just typechecking. */
    "esModuleInterop": true,                        /* Enables emit interoperability between CommonJS and ES Modules via creation of namespace objects for all imports. Implies 'allowSyntheticDefaultImports'. */
    // "preserveSymlinks": true,                    /* Do not resolve the real path of symlinks. */
    // "allowUmdGlobalAccess": true,                /* Allow accessing UMD globals from modules. */

    /* Source Map Options */
    // "sourceRoot": "",                            /* Specify the location where debugger should locate TypeScript files instead of source locations. */
    // "mapRoot": "",                               /* Specify the location where debugger should locate map files instead of generated locations. */
    // "inlineSourceMap": true,                     /* Emit a single file with source maps instead of having a separate file. */
    // "inlineSources": true,                       /* Emit the source alongside the sourcemaps within a single file; requires '--inlineSourceMap' or '--sourceMap' to be set. */

    /* Experimental Options */
    // "experimentalDecorators": true,              /* Enables experimental support for ES7 decorators. */
    // "emitDecoratorMetadata": true,               /* Enables experimental support for emitting type metadata for decorators. */

    /* Advanced Options */
    "skipLibCheck": true,                           /* Skip type checking of declaration files. */
    "forceConsistentCasingInFileNames": true        /* Disallow inconsistently-cased references to the same file. */
  },
  "exclude": [
    "test"
  ]
}

```

5. 新建一个 `src` 文件夹，作为代码存放目录，新建一个 `dist` 文件夹 作为构建编译后的目录。

## 放入写好的类库

1. 放入三个模块作为测试：自定义 log 、 EnumUtils.ts和MathUtils.ts。

```

│  EnumUtils.ts
│  index.ts
│  MathUtils.ts
│
└─log
        CCLogger.ts
        LoggerInterface.ts
        NativeLogger.ts
```

 EnumUtils.ts 

```typescript
export class EnumUtils {
    static getNamesAndValues(e: any) {
        return this.getNames(e).map(_name => { return { name: _name, value: e[_name] as number }; });
    }

    static getNames(e: any) {
        return this.getObjectValues(e).filter(v => typeof v === "string") as string[];
    }

    static getValues(e: any) {
        return this.getObjectValues(e).filter(v => typeof v === "number") as number[];
    }

    private static getObjectValues(e: any): (number | string)[] {
        return Object.keys(e).map(k => e[k]);
    }
}
```

MathUtils.ts

```typescript
/**
 * 数学计算工具类
 */
export class MathUtils {
    /**
     * 弧度制转换为角度值
     * @param radian 弧度制
     * @returns {number}
     */
    public static getAngle(radian: number): number {
        return 180 * radian / Math.PI;
    }

    /**
     * 角度值转换为弧度制
     * @param angle
     */
    public static getRadian(angle: number): number {
        return angle / 180 * Math.PI;
    }

    /**
     * 获取两点间弧度
     * @param p1X
     * @param p1Y
     * @param p2X
     * @param p2Y
     * @returns {number}
     */
    public static getRadian2(p1X: number, p1Y: number, p2X: number, p2Y: number): number {
        var xdis: number = p2X - p1X;
        var ydis: number = p2Y - p1Y;
        return Math.atan2(ydis, xdis);
    }

    /**
     * 获取两点间距离
     * @param p1X
     * @param p1Y
     * @param p2X
     * @param p2Y
     * @returns {number}
     */
    public static getDistance(p1X: number, p1Y: number, p2X: number, p2Y: number): number {
        var disX: number = p2X - p1X;
        var disY: number = p2Y - p1Y;
        var disQ: number = disX * disX + disY * disY;
        return Math.sqrt(disQ);
    }

    public static toFixedStr(value: number, fixCount: number): string {
        return value.toFixed(fixCount).replace(/\.?0*$/, '');
    }

    public static toPercentStr(value: number, fixCount: number): string {
        return this.toFixedStr(value * 100, fixCount) + "%";
    }

    public static toFixedWan(value: number): string {
        const wanFix = 100000;
        let wanFloat = wanFix / 10;
        var v = Math.floor(value / wanFloat) * wanFloat;
        return value > wanFix ? `${MathUtils.toFixedStr(v / wanFix * 10, 1)}万` : value.toString();
    }

    //value = ceil(e*(a*(level^d) + b*(level) + c))
    public static getFinalValueBasedOnParams(level: number, paramList: Array<number>, needCeil?: boolean): number {
        if (paramList.length < 5) {
            return 0;
        }
        let ret = paramList[4] * (paramList[0] * Math.pow(level, paramList[3]) + paramList[1] * level + paramList[2]);
        if (needCeil) {
            ret = Math.ceil(ret);
        }
        return ret;
    }

    /**
     * 获取一个区间的随机数
     * @param $from 最小值
     * @param $end 最大值
     * @returns {number}
     */
    public static limit($from: number, $end: number): number {
        $from = Math.min($from, $end);
        $end = Math.max($from, $end);
        var range: number = $end - $from;
        return $from + Math.random() * range;
    }

    /**
     * 获取一个区间的随机数(帧数)
     * @param $from 最小值
     * @param $end 最大值
     * @returns {number}
     */
    public static limitInteger($from: number, $end: number): number {
        return Math.round(MathUtils.limit($from, $end));
    }

    /**
     * 在一个数组中随机获取一个元素
     * @param arr 数组
     * @returns {any} 随机出来的结果
     */
    public static randomArray(arr: Array<any>): any {
        var index: number = Math.floor(Math.random() * arr.length);
        return arr[index];
    }
}
```

LoggerInterface.ts

```typescript
export interface LoggerInterface {
    /**
     * 初始化日志
     *
     * @param option 参数选项
     */
    init(option: LoggerOption): void;
    log(...args: any[]): void;
    debug(...args: any[]): void;
    info(...args: any[]): void;
    warn(...args: any[]): void;
    error(...args: any[]): void;
}

export interface LoggerOption {
    /**
     * 是否允许输出log
     */
    enableLog: boolean;
}
```

CCLogger.ts

```typescript
import { LoggerInterface, LoggerOption } from "./LoggerInterface";

export  class CCLogger implements LoggerInterface {
    private _option: LoggerOption = null!;

    init(option: LoggerOption): void {
        this._option = option;
    }

    log(...args: any[]): void {
        this._option && this._option.enableLog && console.log.apply(null, args);
    }

    debug(...args: any[]): void {
        this._option && this._option.enableLog && console.debug.apply(null, args);
    }

    info(...args: any[]): void {
        this._option && this._option.enableLog && console.info.apply(null, args);
    }

    warn(...args: any[]): void {
        this._option && this._option.enableLog && console.warn.apply(null, args);
    }

    error(...args: any[]): void {
        this._option && this._option.enableLog && console.error.apply(null, args);
    }
}
```

NativeLogger.ts

```typescript
import { LoggerInterface, LoggerOption } from "./LoggerInterface";
export  class NativeLogger implements LoggerInterface {
    private _option: LoggerOption = null!;

    init(option: LoggerOption): void {
        this._option = option;
    }

    log(...args: any[]) {
        this._option && this._option.enableLog && console.info(this._format(args));
    }

    debug(...args: any[]): void {
        this._option && this._option.enableLog && console.debug(this._format(args));
    }

    info(...args: any[]): void {
        this._option && this._option.enableLog && console.info(this._format(args));
    }

    error(...args: any[]) {
        this._option && this._option.enableLog && console.error(this._format(args));
    }

    warn(...args: any[]) {
        this._option && this._option.enableLog && console.warn(this._format(args));
    }

    /**
     * 原生平台上不能直接打印object和array，因此这里将object和array转换为字符串进行输出，方便在 Android Logcat 中直接看 log 结果
     */
    private _format(...args: any[]): string {
        let msg: string = "";
        args.forEach((value: any, index: number, array: any[]) => {
            if (value == null) {
                msg += "null";
            } else {
                const valType = typeof value;
                if (valType === "string" || valType === "number") {
                    msg += value;
                } else {
                    msg += JSON.stringify(value);
                }
            }
            if (index + 1 < array.length) {
                msg += ",";
            }
        });
        return msg;
    }
}
```

index.ts

```typescript
export {CCLogger} from "./log/CCLogger";
export {NativeLogger} from "./log/NativeLogger";
export {LoggerInterface} from "./log/LoggerInterface";
export {EnumUtils} from "./EnumUtils";
export {MathUtils} from "./MathUtils";
```

## 运行

1. 初始化npm依赖:

```bash
npm install
```

![image-20220613134155965](%E4%BD%BF%E7%94%A8Rolup-js%E6%9E%84%E5%BB%BA%E4%B8%80%E4%B8%AA%E8%87%AA%E5%B7%B1%E7%9A%84%E7%B1%BB%E5%BA%93/image-20220613134155965.png)

成功后 多出一个目录 `node_modules`

2. 如果没有安装 `rollup` 需要安装:

```bash
npm install --global rollup
```

3. 接下来可以运行命令查看：

```bash
# 默认使用rollup.config.js
$ rollup --config

# 或者, 使用自定义的配置文件，这里使用my.config.js作为配置文件
$ rollup --config my.config.js
```

![image-20220613133954889](%E4%BD%BF%E7%94%A8Rolup-js%E6%9E%84%E5%BB%BA%E4%B8%80%E4%B8%AA%E8%87%AA%E5%B7%B1%E7%9A%84%E7%B1%BB%E5%BA%93/image-20220613133954889-16550987977421.png)

更多命令参考 https://www.rollupjs.com/

当然也可以写个shell 脚本 比如 叫做 `build.sh`  内容：`rollup --config`

4. 查看结果：

![image-20220613134333044](%E4%BD%BF%E7%94%A8Rolup-js%E6%9E%84%E5%BB%BA%E4%B8%80%E4%B8%AA%E8%87%AA%E5%B7%B1%E7%9A%84%E7%B1%BB%E5%BA%93/image-20220613134333044.png)

## 测试类库

1. 新建一个`Cocos Creator`项目 `libsbuilddemo`

2. 引入刚刚构建好的 dist 目录下的文件：
3. 在新建的项目 `HelloWorld.ts` 的 `start` 方法 写入测试代码：

```typescript
import {CCLogger, MathUtils} from "../libs/MyLibs";
 
const {ccclass, property} = cc._decorator;
 
@ccclass
export default class Helloworld extends cc.Component {
 
    @property(cc.Label)
    label: cc.Label = null;
 
    @property
    text: string = 'hello';
 
    start () {
        // init logic
        this.label.string = this.text;
 
        let radian = 270
        let angle = MathUtils.getRadian(radian);
        console.log("smile----:" + JSON.stringify(angle));
 
        let logger = new CCLogger()
        logger.init( {enableLog:true} )
        logger.log("日志输出")
        logger.error("错误日志")
 
    }
}
```

4. 查看输出结果：
   ![image-20220613135129571](%E4%BD%BF%E7%94%A8Rolup-js%E6%9E%84%E5%BB%BA%E4%B8%80%E4%B8%AA%E8%87%AA%E5%B7%B1%E7%9A%84%E7%B1%BB%E5%BA%93/image-20220613135129571.png)

以上就是如何使用 rollup.js 构建自己的类库的整个过程。

上面代码链接地址：[点击前往](https://github.com/jsroads/mylibs/tree/main/buildprj)

## 参考

- [**rollup.js 中文文档**](https://www.rollupjs.com/)
- [import自己写的库错误，如何使用ts正确的写一个npm库？](https://forum.cocos.org/t/topic/117548)
- [参考项目GitHub链接](https://github.com/xuanyangyang/ts-demo)

