---
title: 给JS包写TypeScript用的类型申明文件
categories: tool
tags:
  - ts
abbrlink: 8e083b5b
date: 2018-08-13 19:56:55
---

## 给JS包写TypeScript用的类型申明文件

TS (TypeScript)区别于JS (JavaScript)一个最大的不同是TS增加了类型。当一些TS代码要使用JS包的时候，最好这些JS包都有类型介绍，比如这个变量是什么类型，那个函数参数的什么类型，返回什么类型参数等等，这个Type Declaration File有点像是C++的头文件，定义了JS包的接口信息，这个文件的后缀是.d.ts

<!-- more -->

如果让我们从头手写一个JS包的.d.ts文件会比较痛苦，因为JS包本身就写得很乱，有定义了很多对象，有时候要理清他们之间的关系就是头疼的事情，特别是如果JS包很大的话，那基本上很花时间了。

那么有两个解决方案，在npm包管理中，有一类@types/package的包，就是别人已经写好了的对应package的.d.ts文件。我们只用使用npm install --save @types/package就行了。

- 官网提供了一些常用的类库的声明文件

这个网站能搜到@types包含了哪些JS的包，一些常用的基本都能搜索到，下载下来直接用就行了
https://microsoft.github.io/TypeSearch/

[DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped)

如果这上面没有，一些比较冷门的JS包怎么办呢？
那就得自己写，自己写可以参考TS官网关于怎么写的说明文档
https://www.typescriptlang.org/docs/handbook/declaration-files/introduction.html

- 自己用工具或者命令去生成

另外，我发现一个很好的工具dts-gen，
https://github.com/Microsoft/dts-gen
根据它的说明，安装好了之后，它会针对目标JS包自动生成一个最基本款的.d.ts文件，帮你理清目标JS包的结构，自己在对于这个文件做进一步的细化和修改。
这个特别好，但是有时候对于一些特别复杂的JS包，它会抛出异常，目前看是应该属于bug了，总的来说应该比没有要好很多。

#### 用dtsmake 生成 声明文件

- 先全局安装

```bash
npm i dtsmake -g
npm i tern -g
npm i tern --save-dev
dtsmake -s fileame.js
```

- 然后在本地磁盘 自己新建一个目录作为转换的项目 比如 “test”
- 然后本地安装 npm

```bash
npm i dtsmake --save-dev
npm i tern --save-dev
dtsmake -s fileame.js
```

- 如果 本地`no such file or directory, open 'F:\test\package.json`错误。
- 错误原因：项目没有package.json文件导致 解决方案：使用 `npm init -y`  生成默认的package.json文件



注意如果是闭包结构，要删除闭包结构**(当然如果你在闭包情况下 能完美生成 就不用删除闭包结构啦)

### 若 npm命令有问题 可以 用 淘宝镜像的 cnpm 命令代替 npm 命令  

只是 每个命令前面多了一个 c 就是  比如  `npm i tern --save-dev` 变成 `cnpm i tern --save-dev`

如果 有关于权限问题的报错 请 前面 加上 `sudo`  输入密码 获得权限即可。



关于 cnpm 和 sudo 权限  不在 本教程讨论

#### 其他相关拓展

还有几种可用的选项供您选择。

##### 也许你不需要一个

TypeScript现在支持该`--allowJs`标志，并将在.js文件中进行更多基于JS的推断。您可以尝试在编译中包括.js文件以及`--allowJs`设置，以查看是否为您提供了足够好的类型信息。TypeScript可以识别这些文件中的ES5样式类和JSDoc注释之类的东西，但是如果库以一种奇怪的方式初始化自身，它可能会被绊倒。

##### 开始使用 `--allowJs`

如果`--allowJs`给您不错的结果，并且您想自己编写一个更好的定义文件，则可以将其`--allowJs`与`--declaration`TypeScript对库类型的“最佳猜测” 结合使用。这将为您提供一个不错的起点，并且如果JSDoc注释写得很好并且编译器能够找到它们，那么它可能与手写文件一样好。

##### 自己写声明文件

如果您只是想稍后再做，并且暂时不用输入类型，现在可以在TypeScript 2.0中编写

```js
declare module "temp";
```

这将使您使用类型`import`为的`"temp"`模块`any`。如果您有一家跨国公司要稍后处理，只需写

```js
declare const temp: any;
```

这会给你一个`temp`变量。

##### 如果是typescript项目

`--declarations`生成`.js`文件和`.d.ts`文件，这意味着您只需要运行一个编译即可。

### 相关工具

下载 GitHub 文件夹的在线地址：[DownGit](https://minhaskamal.github.io/DownGit)

