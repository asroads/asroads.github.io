---
title: Rollupjs构建JavaScript项目初试牛刀
comments: true
categories: tool
tags:
  - js
  - rollupjs
  - md5
abbrlink: bdcf7fdc
date: 2021-07-01 12:50:57
---

游戏开发过程中有时候难免会引入第三方类库，这些年随着Javascript发展的迅速，加上Typescript后来居上的趋势，前端变得越来越受到开发者的青睐，有时候我们去官网或者下载类库的时候，官方给出的源码是没有打包的源码，需要我们自己初始化环境，编译成自己想要的格式或者标准。今天就说说其中的一个[rollupjs](https://www.rollupjs.com/) 构建。
<!--more-->
我们先看看 中文官网介绍：

![rollup.js logo](Rollupjs构建JavaScript项目初试牛刀/twitter-card-20210701131620791.jpg)

> [Rollup](https://www.rollupjs.com/) 是一个 JavaScript 模块打包器，可以将小块代码编译成大块复杂的代码。Rollup 对代码模块使用新的标准化格式，这些标准都包含在 JavaScript 的 ES6 版本中，而不是以前的特殊解决方案，如 CommonJS 和 AMD。ES6 模块可以使你自由、无缝地使用你最喜爱的 library 中那些最有用独立函数，而你的项目不必携带其他未使用的代码。

就是说，[Rollup](https://www.rollupjs.com/) 可以帮助我们构建多个js 生成一个js 文件来使用，这个在很多类库特别常见。简单的说构建方式是两种，一个是命令行构建，一个是配置文件构建

### 命令行的参数(Command line flags)

```bash
-i, --input <filename>      要打包的文件（必须）
-o, --file <output>         输出的文件 (如果没有这个参数，则直接输出到控制台)
-f, --format <format>       输出的文件类型 (amd, cjs, esm, iife, umd)
-e, --external <ids>        将模块ID的逗号分隔列表排除
-g, --globals <pairs>       以`module ID:Global` 键值对的形式，用逗号分隔开 
                              任何定义在这里模块ID定义添加到外部依赖
-n, --name <name>           生成UMD模块的名字
-h, --help                  输出 help 信息
-m, --sourcemap             生成 sourcemap (`-m inline` for inline map)
--amd.id                    AMD模块的ID，默认是个匿名函数
--amd.define                使用Function来代替`define`
--no-strict                 在生成的包中省略`"use strict";`
--no-conflict               对于UMD模块来说，给全局变量生成一个无冲突的方法
--intro                     在打包好的文件的块的内部(wrapper内部)的最顶部插入一段内容
--outro                     在打包好的文件的块的内部(wrapper内部)的最底部插入一段内容
--banner                    在打包好的文件的块的外部(wrapper外部)的最顶部插入一段内容
--footer                    在打包好的文件的块的外部(wrapper外部)的最底部插入一段内容
--interop                   包含公共的模块（这个选项是默认添加的）
```

此外，还可以使用以下参数：

###### `-h`/`--help`

打印帮助文档。

###### `-v`/`--version`

打印已安装的Rollup版本号。

###### `-w`/`--watch`

监听源文件是否有改动，如果有改动，重新打包

###### `--silent`

不要将警告打印到控制台。

### 配置文件(Configuration files)

Rollup的配置文件是可选的，但是使用配置文件的作用很强大，而且很方便，因此我们推荐你使用

配置文件是一个ES6模块，它对外暴露一个对象，这个对象包含了一些Rollup需要的一些选项。通常，我们把这个配置文件叫做`rollup.config.js`，它通常位于项目的根目录

如果你想使用Rollup的配置文件，记得在命令行里加上`--config`或者`-c`

```bash
# 默认使用
$ rollup.config.js$ rollup --config
# 或者, 使用自定义的配置文件，这里使用my.config.js作为配置文件
$ rollup --config my.config.js
```

常用的核心配置输入(input *`-i`/`--input`*)

> `String` 这个包的入口点 (例如：你的 `main.js` 或者 `app.js` 或者 `index.js`)
>
> #### 文件(file *`-o`/`--output.file`*)
>
> `String` 要写入的文件。也可用于生成 sourcemaps，如果适用
>
> #### 格式(format *`-f`/`--output.format`*)
>
> `String` 生成包的格式。 下列之一:
>
> - `amd` – 异步模块定义，用于像RequireJS这样的模块加载器
> - `cjs` – CommonJS，适用于 Node 和 Browserify/Webpack
> - `esm` – 将软件包保存为 ES 模块文件，在现代浏览器中可以通过 `<script type=module>` 标签引入
> - `iife` – 一个自动执行的功能，适合作为`<script>`标签。（如果要为应用程序创建一个捆绑包，您可能想要使用它，因为它会使文件大小变小。）
> - `umd` – 通用模块定义，以`amd`，`cjs` 和 `iife` 为一体
> - `system` - SystemJS 加载器格式
>
> #### 生成包名称(name *`-n`/`--name`*)
>
> `String` 变量名，代表你的 `iife`/`umd` 包，同一页上的其他脚本可以访问它。

完整配置地址：[大选项列表](https://www.rollupjs.com/guide/big-list-of-options)

### Md5例子

学习一个工具最好的方式，就是拿例子来一遍，下面我们就用一个MD5算法的项目说明一下：[Md5 typescript](https://www.npmjs.com/package/md5-typescript)

我们下载项目后 项目地址[点击前往](https://github.com/Hipparch/Md5-typescript) 目录如下：

```bash
├── LICENSE
├── README.md
├── dist
|  ├── bundles
|  |  ├── bundle.umd.js
|  |  └── bundle.umd.min.js
|  ├── index.d.ts
|  ├── index.js
|  ├── index.js.map
|  └── index.metadata.json
├── index.ts
├── package.json
├── rollup.config.js
└── tsconfig.json

directory: 2 file: 12
```

此时 我们可以查看 `tsconfig.json` 修改我们的配置 成功后运行 `tsc`  命令

#### 修改 rollup.config.js

修改这个配置，这里如下修改：

```javascript
const input = 'dist/index.js';
const defaultName = 'MD5';
export default {
    input: input,
    output: [
        {
            file: 'dist/bundle/md5.browser.umd.js',
            format: 'umd',
            name: defaultName,
            sourcemap: false,
        },
        {
            file: 'dist/bundle/md5.browser.esm.js',
            format: 'es',
            name: defaultName,
            sourcemap: false,
        },
        {
            file: 'dist/bundle/md5.browser.cjs.js',
            format: 'cjs',
            name: defaultName,
            sourcemap: false,
        },
        {
            file: 'dist/bundle/md5.js',
            format: 'iife',
            name: defaultName,
            sourcemap: false,
        }
    ]
}
```

然后运行命令：

```bash
 rollup -c
```

输出如下：

```bash
smile@bogon Md5-typescript-master % rollup -c

dist/index.js → dist/bundle/md5.browser.umd.js, dist/bundle/md5.browser.esm.js, dist/bundle/md5.browser.cjs.js, dist/bundle/md5.js...
created dist/bundle/md5.browser.umd.js, dist/bundle/md5.browser.esm.js, dist/bundle/md5.browser.cjs.js, dist/bundle/md5.js in 93ms
smile@bogon Md5-typescript-master % 
```

```bash
├── .idea
|  ├── .gitignore
|  ├── Md5-typescript-master.iml
|  ├── modules.xml
|  └── workspace.xml
├── LICENSE
├── README.md
├── dist
|  ├── bundle
|  |  ├── md5.browser.cjs.js
|  |  ├── md5.browser.esm.js
|  |  ├── md5.browser.umd.js
|  |  └── md5.js
|  ├── bundles
|  |  ├── bundle.umd.js
|  |  └── bundle.umd.min.js
|  ├── index.d.ts
|  ├── index.js
|  ├── index.js.map
|  └── index.metadata.json
├── index.ts
├── package.json
├── rollup.config.js
└── tsconfig.json

directory: 4 file: 20
```

其中：

```
|  |  ├── md5.browser.cjs.js
|  |  ├── md5.browser.esm.js
|  |  ├── md5.browser.umd.js
|  |  └── md5.js
```

这几个文件就是我们最终生成的文件  如果是作为第三方库 导入 `Typescript` 项目 就带上 声明文件 `index.d.ts` 即可。

用法：

```typescript
import {Md5} from "md5-typescript";
console.log(Md5.init('test'));
```

更多加密算法参考：[crypto-js](https://www.npmjs.com/package/crypto-js) 

###  拓展知识

#### MD5简介

MD5是一种信息摘要算法（Message-Digest Algorithm），可以产生出一个128位（8位(bit)=1字节(byte),16字节）的散列值（hash）.

```typescript
12345   MD5===> e10adc3949ba59abbe56e057f20f883e
// 说好的128位,为什么有32个字符.
//这串码是16进制表示,1位==4位二进制.
1
12345   MD5===> e10adc3949ba59abbe56e057f20f883e
2
// 说好的128位,为什么有32个字符.
3
//这串码是16进制表示,1位==4位二进制.
```

#### MD5 加密后的类型（16位与 32位的区别）

MD5 加密后的位数一般为两种，16 位与 32 位。16 位实际上是从 32 位字符串中，取中间的第 9 位到第 24 位的部分,MD5 加密后的字符串又分为大写与小写两种，也就是其中的字母是大写还是小写。

```typescript
let md5_16 = md5_32.substring(8, 24);
```

例如：

| 字符串   | 123456                           |
| -------- | -------------------------------- |
| 16位小写 | 49ba59abbe56e057                 |
| 16位大写 | 49BA59ABBE56E057                 |
| 32位小写 | e10adc3949ba59abbe56e057f20f883e |
| 32位大写 | E10ADC3949BA59ABBE56E057F20F883E |

### 参考地址

- [10分钟快速入门rollup.js](https://zhuanlan.zhihu.com/p/50048269)
- [rollupjs中文文档](https://www.rollupjs.com/)
- [一文读懂 MD5 算法](https://segmentfault.com/a/1190000021691476)

