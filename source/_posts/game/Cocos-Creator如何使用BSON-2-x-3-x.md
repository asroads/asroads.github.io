---
title: Cocos Creator如何使用BSON|2.x|3.x
comments: true
categories: game
tags:
  - Cocos
  - bson
abbrlink: df79c317
date: 2021-06-08 16:21:42
---

[BSON](https://bsonspec.org/)是一种二进制数据格式，官方解释是一种二进制格式的[JSON](http://json.org/)，在传输中由于数据被压缩，具有体积小，修改速度快等优势。故而得到一些应用和开发中的青睐，下面介绍一下如何在[Cocos Creator](https://www.cocos.com/)里面引入[BSON](https://bsonspec.org/)
<!--more-->

## BSON简单介绍

首先我们去官方网站，[JSON and BSON](https://www.mongodb.com/json-and-bson) 找到一些关于 `JSON` 和`BSON`的介绍和对比：

### JSON vs BSON

|              | **JSON**                       | **BSON**                                                     |
| :----------- | ------------------------------ | ------------------------------------------------------------ |
| Encoding     | UTF-8 String                   | Binary                                                       |
| Data Support | String, Boolean, Number, Array | String, Boolean, Number (Integer, Float, Long, Decimal128...), Array, Date, Raw Binary |
| Readability  | Human and Machine              | Machine Only                                                 |

了解一些信息后 我们打开 [BSON 官网](https://bsonspec.org/) 

![image-20210608162835917](Cocos-Creator如何使用BSON-2-x-3-x/image-20210608162835917.png)

然后可以看到很多语言的支持情况，这里我们选择 

- Node.js
  - [bson](https://github.com/mongodb/js-bson) - BSON serialization and deserialization library used by the official MongoDB Node.js driver.

接下来是具体步骤：

## 步骤

### 下载编译js-bson

1. 首先外面[js-bson](https://github.com/mongodb/js-bson) 到本地

![image-20210608163150480](Cocos-Creator如何使用BSON-2-x-3-x/image-20210608163150480.png)

2. 编译项目，生成需要的类库，需要node 环境 和 tsc 环境 

   ```shell
   npm install
   npm run build
   ```

   

### 修改生成后的文件

1. 首先我们找到 `dist` 文件夹 下的 `bson.bundle.js` 然后 修改 

原代码：

```javascript
var BSON = (function (exports) {
	'use strict';
  ......
  
  return exports;

}({}));
```

修改后：

```javascript
window.bson = (function (exports) {
	'use strict';
	'use strict';
  ......
  
  return exports;

}({}));
```

2. 然后我们找到根目录下生成的 bson.d.ts 声明文件 

原代码：

```typescript
export declare class Binary {
    _bsontype: 'Binary';
}
/** @public */
export declare type UUIDExtended = {
    $uuid: string;
};
export {};
```

修改后：

```typescript
declare global {
  declare namespace bson {
		export declare class Binary {
    	_bsontype: 'Binary';
		}
		/** @public */
		export declare type UUIDExtended = {
    		$uuid: string;
		};
  }
}
export {};
```

### 测试结果

修改完毕后 测试一下我们的结果：

```typescript
 const doc = { long: bs.Long.fromNumber(100) };
 const data = bs.serialize(doc);
 console.log('data:', data);
```

结果如下

![image-20210608164626005](Cocos-Creator如何使用BSON-2-x-3-x/image-20210608164626005.png)

本方法适合 `Cocos Creator 2.x` 和 `Cocos Creator 3.1.x`

最后 项目源码 地址  

- [Cocos Creator 2.x demo](https://github.com/jsroads/mylibs/tree/main/bson/2.4.x)
- [Cocos Creator 3.1.x demo](https://github.com/jsroads/mylibs/tree/main/bson/3.1.x)



## 总结

目前还没有真正理解 Cocos Creator 关于第三方 node库的怎么最优雅的方式使用，后续工作中会再深入探索和研究，查漏补缺的学习，这次先做个简单的引入。