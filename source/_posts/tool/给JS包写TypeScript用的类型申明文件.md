---
title: 给JS包写TypeScript用的类型申明文件
date: 2018-08-13 19:56:55
categories: "tool"
tags:
- Typescript
---

### 给JS包写TypeScript用的类型申明文件

TS (TypeScript)区别于JS (JavaScript)一个最大的不同是TS增加了类型。当一些TS代码要使用JS包的时候，最好这些JS包都有类型介绍，比如这个变量是什么类型，那个函数参数的什么类型，返回什么类型参数等等，这个Type Declaration File有点像是C++的头文件，定义了JS包的接口信息，这个文件的后缀是.d.ts

<!-- more -->

如果让我们从头手写一个JS包的.d.ts文件会比较痛苦，因为JS包本身就写得很乱，有定义了很多对象，有时候要理清他们之间的关系就是头疼的事情，特别是如果JS包很大的话，那基本上很花时间了。

那么有两个解决方案，在npm包管理中，有一类@types/package的包，就是别人已经写好了的对应package的.d.ts文件。我们只用使用npm install --save @types/package就行了。
这个网站能搜到@types包含了哪些JS的包，一些常用的基本都有
https://microsoft.github.io/TypeSearch/
如果这上面没有，一些比较冷门的JS包怎么办呢？
那就得自己写，自己写可以参考TS官网关于怎么写的说明文档
https://www.typescriptlang.org/docs/handbook/declaration-files/introduction.html
另外我发现一个很好的工具dts-gen，
https://github.com/Microsoft/dts-gen
根据它的说明，安装好了之后，它会针对目标JS包自动生成一个最基本款的.d.ts文件，帮你理清目标JS包的结构，自己在对于这个文件做进一步的细化和修改。
这个特别好，但是有时候对于一些特别复杂的JS包，它会抛出异常，目前看是bug了，总之比没有强很多



#### 也可以用dtsmake

```bash
npm i dtsmake -g
npm i tern --save-dev
dtsmake -s fileame.js
```

**注意如果是闭包结构，要删除闭包结构**



### 如果 npm命令有问题 可以 用 淘宝镜像的 cnpm 命令代替 npm 命令  

只是 每个命令前面多了一个 c 就是  比如  `npm i tern --save-dev` 变成 `cnpm i tern --save-dev`

如果 有关于权限问题的报错 请 前面 加上 `sudo`  输入密码 获得权限即可。



关于 cnpm 和 sudo 权限  不在 本贴讨论