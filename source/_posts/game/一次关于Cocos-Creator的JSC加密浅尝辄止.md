---
title: 一次关于Cocos Creator的JSC加密浅尝辄止
comments: true
categories: game
tags:
  - Cocos
  - xxtea
abbrlink: 321d82c3
date: 2021-07-06 15:31:45
---

Cocos Creator 在构建的时候支持对脚本进行加密和压缩，官方没有直接提供解密方法，论坛和网络上有其他高手给出了一些解决方案，本文介绍了，加密的类库和Cocos Creator 工作流程，以及加密的意义。
<!--more-->

### 演示环境

首先我们本本要用到的一些类库和地址：

- [xxtea-node](https://www.npmjs.com/package/xxtea-node) node环境的一个 [xxtea](https://en.wikipedia.org/wiki/XXTEA) 加密类库
- [xxtea-js](https://github.com/xxtea/xxtea-js) a XXTEA library for JavaScript 可以脱离`node`环境的 `xxtea` 类库

如果我们的环境有nodejs 环境建议使用 [xxtea-node](https://www.npmjs.com/package/xxtea-node)  如果没有 只能支持 js 那就用 [xxtea-js](https://github.com/xxtea/xxtea-js)  如果说`python`环境 可以尝试 [OEDx](https://github.com/OEDx) 大佬的这个[cocos-jsc-endecryptor](https://github.com/OEDx/cocos-jsc-endecryptor) 脚本。

### [xxtea-node] 介绍

#### encrypt 加密

```javascript
var xxtea = require('xxtea-node');
var str = "Hello World! 你好，中国！";
var key = "1234567890";
var encrypt_data = xxtea.encrypt(xxtea.toBytes(str), xxtea.toBytes(key));
console.log(new Buffer(encrypt_data).toString('base64'));
```

#### decrypt 解密

```javascript
var decrypt_data = xxtea.toString(xxtea.decrypt(encrypt_data, xxtea.toBytes(key)));
console.assert(str === decrypt_data);
```

### [xxtea-js ] 介绍

`encrypt` 加密 & `decrypt` 解密

```javascript
 var str = "Hello World! 你好，中国！";
 var key = "1234567890";
 var encrypt_data = XXTEA.encryptToBase64(str, key);
 console.log(encrypt_data);
 var decrypt_data = XXTEA.decryptFromBase64(encrypt_data, key);
 console.assert(str === decrypt_data);
```

### 批处理

下面给出 [luckyaibin](https://github.com/luckyaibin) 大佬给出的 开箱即用的 `nodejs` 脚本 脚本地址：[点击前往](https://github.com/luckyaibin/cocoscreatorjscdecrypt)

需要 nodejs 环境 安装命令：`npm install xxtea-node npm install pako`

我自己安装的时候 还提示需要 `md5-file` 这个依赖，按照提示安装即可。

下面给出我测试用的  `crack.js` 文件内容

```javascript
const md5File = require('md5-file')
var fs = require("fs");
var path = require("path")
var pako = require("pako")
var xxtea = require("xxtea-node");

//这里填写 遍历的目录路径
//var FILEPATH = path.resolve('./../cn.isir.jz2/');
var FILEPATH = path.resolve('/Users/smile/Downloads/testx');
var KEY = "18237418234-f3a3-4b"  //cocoscreator 的 工程加密key
var UNZIP = true              //是否启用压缩

//不带后缀的全路径名
function getFullFileNameNoSuffix(fullFileName){
	var fullFileNameNoSuffix = fullFileName.substring(0,fullFileName.lastIndexOf("."));
	return fullFileNameNoSuffix
}

function getFileMD5(filename){
	const hash = md5File.sync(filename)
	console.log(`The MD5 sum of ${filename} is: ${hash}`)
	return hash;
}
function xxteaDecode(filename){
		var data;
		try{
			data =  fs.readFileSync(filename)
		}catch(error){
			console.log("读取文件失败",filename);
            return
		}
        var res = xxtea.decrypt(data,xxtea.toBytes(KEY))
        if(res == null){
            console.log("解密失败")
            return
        }

        if(UNZIP) {
            console.log("开始解压", filename)
            res = pako.ungzip(res)
        }
        var newName = getFullFileNameNoSuffix(filename) + ".js"

		try{
			fs.writeFileSync(newName,res)
		}
		catch(error){
            console.log(newName,"写入出错")
            return
        }
		console.log("写入完毕:",newName)
    
}

function xxteaEncode(filename){
		var data;
		try{
			data =  fs.readFileSync(filename)
		}catch(error){
			console.log("读取文件失败",filename);
            return
		}
        var res;
        if(UNZIP) {
            console.log("开始压缩", filename)
            res = pako.gzip(data,{ level:6})//不同等级压缩前后大小都和之前的不一样
        }else{
			res = data
		}
        res = xxtea.encrypt(res,xxtea.toBytes(KEY))
        if(res == null){
            console.log("加密失败")
            return
        }
        var newName = getFullFileNameNoSuffix(filename) + ".jsc"
        try{
			fs.writeFileSync(newName,res)
		}
		catch(error){
            console.log(newName,"写入出错")
            return
        }
		console.log("写入完毕:",newName)
		//getFileMD5(newName){
}

//文件遍历方法
function fileDisplay(filePath,op){
    //根据文件路径读取文件，返回文件列表
    fs.readdir(filePath,function(err,files){
        if(err){
            console.warn(err)
        }else{
            //遍历读取到的文件列表
            files.forEach(function(filename){
                //获取当前文件的绝对路径
                var filedir = path.join(filePath, filename);
                //根据文件路径获取文件信息，返回一个fs.Stats对象
                fs.stat(filedir,function(eror, stats){
                    if(eror){
                        console.warn('获取文件stats失败');
                    }else{
                        var isFile = stats.isFile();//是文件
                        var isDir = stats.isDirectory();//是文件夹
                        if(isFile){
							var suffix = path.extname(filedir);
							if (suffix == ".jsc" || suffix == ".js" ){
								if (op=="d" && suffix == ".jsc" ){
									console.log(filedir,"解密");
									xxteaDecode(filedir)
									fs.unlinkSync(filedir)
								}
								else if(op=="e" && suffix == ".js"){
									console.log(filedir,"加密");
									xxteaEncode(filedir)
									fs.unlinkSync(filedir)
								}
								else{
									console.log("invalid",filedir)
								}
								
							}
　　　　　　　　　　　　　　
                        }
                        if(isDir){
							//console.log("进入目录:",filedir)
                            fileDisplay(filedir,op);//递归，如果是文件夹，就继续遍历该文件夹下面的文件
                        }
                    }
                })
            });
        }
    });
}

const [node, path0, ...argv] = process.argv;
var op = argv[0]
fileDisplay(FILEPATH,op);
```

加密命令：

```bash
node crack.js e
```

输出结果：

```bash
smile@bogon nodetestdemo % node crack.js e
/Users/smile/Downloads/testx/output.js 加密
开始压缩 /Users/smile/Downloads/testx/output.js
写入完毕: /Users/smile/Downloads/testx/output.jsc
/Users/smile/Downloads/testx/Browser.js 加密
开始压缩 /Users/smile/Downloads/testx/Browser.js
写入完毕: /Users/smile/Downloads/testx/Browser.jsc
smile@bogon nodetestdemo % 
```

![image-20210706155731373](一次关于Cocos-Creator的JSC加密浅尝辄止/image-20210706155731373.png)

解密命令：

```bash
node crack.js d
```

输出结果：

```bash
smile@bogon nodetestdemo % node crack.js d
/Users/smile/Downloads/testx/Browser.jsc 解密
开始解压 /Users/smile/Downloads/testx/Browser.jsc
写入完毕: /Users/smile/Downloads/testx/Browser.js
/Users/smile/Downloads/testx/output.jsc 解密
开始解压 /Users/smile/Downloads/testx/output.jsc
写入完毕: /Users/smile/Downloads/testx/output.js
smile@bogon nodetestdemo % 
```

<img src="一次关于Cocos-Creator的JSC加密浅尝辄止/image-20210706160538778.png" alt="image-20210706160538778" style="zoom:150%;" />

以上就是 关于`xxxtea` 使用`node` 命令 批量 加密 解密的演示。

### 其他思路

下面给出笔者看到的其他大佬的思路

- CSDN [小宇文](https://blog.csdn.net/XiaoYuWen1242466468) 的一个思路： [XXTea加密 c++ 与 js 互相加解密](https://blog.csdn.net/XiaoYuWen1242466468/article/details/78274891)
- [OEDx](https://github.com/OEDx)/**[cocos-jsc-endecryptor](https://github.com/OEDx/cocos-jsc-endecryptor)**
- 论坛 [Cocos Creator 一种「无侵入」资源加密方案](https://forum.cocos.org/t/cocos-creator/95492)

### 加密的意义

前端加密往往上伴随混淆代码，套壳，特定的加盐处理自定义的一些非公开算法决定，加密资源往往都会带来解密的消耗，往往来说加密和混淆越厉害，解密的代价就越大，相对来讲资源被破解的成本越大，作为游戏来说，简单的加密往往能拦住大部分的恶意破解或者外挂助手，但是本质上这些都防不住解密高手，对于高手来讲，就如同开锁，只是成本和时间问题。笔者态度，在不影响性能的情况下，适度加密，往往起到对资源和代码的保护作用，这些精力是值得的，但切为了安全，忽视了用户体验，造成得不偿失的结果。

### 参考

- [cocoscreatorjscdecrypt开箱即用的方法](https://github.com/luckyaibin/cocoscreatorjscdecrypt)
- [XXTEA for JavaScript](https://github.com/xxtea/xxtea-js)
- [XXTEA for Node.js](https://www.npmjs.com/package/xxtea-node)
- [适用于 Cocos 的 JSC 加解密工具](https://oedx.github.io/2018/09/14/jsc-encryption-and-decryption-tools-for-cocos/)

