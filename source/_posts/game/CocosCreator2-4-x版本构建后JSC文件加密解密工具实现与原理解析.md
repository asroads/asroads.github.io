---
title: CocosCreator2.4.x版本构建后JSC文件加密解密工具实现与原理解析
comments: true
categories: game
tags:
  - Cocos
  - JSC
abbrlink: 2d737442
date: 2024-12-16 12:12:02
---

在前端游戏开发中，特别是在游戏开发或一些商业应用中，我们经常需要对JavaScript源代码进行保护，防止代码被轻易查看或修改。`JSC（JavaScript Compiled）`文件是一种常见的`JavaScript`代码保护方式，它通过加密和压缩技术将原始JS代码转换为难以阅读的格式。我们知道`Cocos Creator2.4.x` 构建后如果选择压缩模式，会生成`JSC`后缀的压缩文件，那么我们如何解析`JSC`文件呢？本文将介绍一个基于Node.js的`JSC`文件加密解密工具的实现原理和使用方法。该工具使用`XXTEA`加密算法和`Pako`压缩库，能够将普通的`JavaScript`文件加密为`JSC`文件，也能将JSC文件解密回原始的`JavaScript`代码。

<!--more-->

## 技术栈介绍

- **Node.js**：运行环境
- **xxtea-node**：提供XXTEA加密算法的实现
- **pako**：提供高效的压缩和解压缩功能
- **readline-sync**：处理命令行交互输入
- **fs**和**path**：Node.js内置模块，用于文件系统操作

### XXTEA加密算法

XXTEA（Corrected Block TEA）是一种分组密码算法，由剑桥大学计算机实验室的David Wheeler和Roger Needham设计。它是TEA（Tiny Encryption Algorithm）的改进版本，具有以下特点：

- 简单高效：算法结构简单，实现容易，运行速度快
- 安全性较高：虽然不如AES等现代加密算法，但对于代码保护已足够
- 密钥长度灵活：通常使用128位密钥
- 资源消耗低：适合在资源受限的环境中使用

### Pako压缩库

Pako是一个高性能的JavaScript压缩库，是zlib的JavaScript移植版本。在本项目中，我们使用Pako来：

- 减小加密后文件的大小
- 增加代码的混淆程度，提高破解难度
- 提高加载效率

## 核心功能实现

### 文件操作工具

首先，我们实现了基本的文件读写功能：

```javascript
// 文件操作工具
function writeContentToFile(filePath, content) {
    fs.writeFileSync(filePath, content);
}

function readContentFromFile(filePath) {
    return fs.readFileSync(filePath);
}
```

### 压缩与解压缩

使用Pako库实现文件内容的压缩和解压缩：

```javascript
// 压缩文件
function compress(content) {
    return pako.deflate(content);
}

// 解压文件
function decompress(content) {
    try {
        return pako.inflate(content);
    } catch (e) {
        console.error("Error: Decompression failed. Please check your input.");
        throw e;
    }
}
```

### 加密与解密

结合XXTEA加密算法和压缩功能，实现核心的加密解密逻辑：

```javascript
// 加密文件
function encryptContent(content, key, isZip) {
    const compressed = isZip ? compress(content) : content;
    const encrypted = xxtea.encrypt(compressed, xxtea.toBytes(key));
    if (!encrypted) throw new Error("Encryption failed.");
    return encrypted;
}

// 解密文件
function decryptContent(content, key, isZip) {
    const decrypted = xxtea.decrypt(content, xxtea.toBytes(key));
    const decoded = isZip ? decompress(decrypted) : content;
    if (!decoded) throw new Error("Decryption failed. Check your key.");
    return decoded;
}
```

这里的加密流程是：

1. 根据`isZip`参数决定是否先压缩内容
2. 使用XXTEA算法和提供的密钥对内容进行加密

解密流程则是加密的逆过程：

1. 使用XXTEA算法和密钥解密内容
2. 如果内容是压缩的，则进行解压缩

### 文件加密与解密功能

基于上述核心功能，我们实现了完整的文件加密和解密功能：

```javascript
// 解密逻辑
function decrypt(isZip, key, jscPath) {
    console.log("Begin decrypt.");

    if (!fs.existsSync(jscPath)) {
        console.error("Error: The specified .jsc file does not exist.");
        return false;
    }

    const encryptedContent = readContentFromFile(jscPath);
    let decryptedContent;
    try {
        decryptedContent = decryptContent(encryptedContent, key, isZip);
    } catch (e) {
        console.error(e.message);
        return false;
    }

    const outputDir = "decryptOutput";
    if (!fs.existsSync(outputDir)) fs.mkdirSync(outputDir);

    const decryptedFilePath = path.join(outputDir, "dec.js");
    writeContentToFile(decryptedFilePath, decryptedContent);

    console.log("Decryption successful. Check the decryptOutput directory.");
    return true;
}

// 加密逻辑
function encrypt(isZip, key, jsPath) {
    console.log("Begin encrypt.");

    if (!fs.existsSync(jsPath)) {
        console.error("Error: The specified .js file does not exist.");
        return false;
    }

    const jsContent = readContentFromFile(jsPath);
    let encryptedContent;
    try {
        encryptedContent = encryptContent(jsContent, key, isZip);
    } catch (e) {
        console.error(e.message);
        return false;
    }

    const outputDir = "encryptOutput";
    if (!fs.existsSync(outputDir)) fs.mkdirSync(outputDir);

    const encryptedFilePath = path.join(outputDir, "projectChanged.jsc");
    writeContentToFile(encryptedFilePath, encryptedContent);

    console.log("Encryption successful. Check the encryptOutput directory.");
    return true;
}
```

这两个函数分别处理了：

- 文件存在性检查
- 读取源文件内容
- 调用核心加密/解密函数
- 创建输出目录
- 写入处理后的文件
- 错误处理和日志输出

## 命令行交互

为了方便用户使用，我们实现了命令行交互功能，支持通过命令行参数或交互式输入来指定操作类型、文件路径和密钥：

```javascript
// 主程序入口
(() => {
    const args = process.argv.slice(2);
    const command = args[0];
    const options = {
        path: args[1], nozip: args.includes("--nozip"), key: args[2] || "",
    };

    if (!command || !["encrypt", "decrypt"].includes(command)) {
        console.log("Usage: node decjsc.js <encrypt|decrypt> <path> <key> [--nozip]");
        process.exit(1);
    }

    const isZip = !options.nozip;
    // 使用 readline-sync 获取用户输入
    const key = options.key || readlineSync.question("请输入加密或解密的工程加密key 格式:xxxxxxxx-xxxx-xx: ");
    const filePath = options.path || readlineSync.question("请输入加密或者解密 .jsc/.js 的路径: ");

    if (command === "decrypt") {
        decrypt(isZip, key, filePath);
    } else if (command === "encrypt") {
        encrypt(isZip, key, filePath);
    }
})();
```

命令行支持的参数包括：

- 操作类型：`encrypt`（加密）或`decrypt`（解密）
- 文件路径：要处理的JS或JSC文件路径
- 密钥：用于加密或解密的密钥
- 选项：`--nozip`表示不使用压缩

如果用户没有通过命令行提供这些参数，程序会通过交互式提示要求用户输入。

最后完整代码：

```javascript
const fs = require("fs");
const path = require("path");
const pako = require("pako");
const xxtea = require("xxtea-node");
const readlineSync = require('readline-sync');

// 文件操作工具
function writeContentToFile(filePath, content) {
    fs.writeFileSync(filePath, content);
}

function readContentFromFile(filePath) {
    return fs.readFileSync(filePath);
}

// 压缩文件
function compress(content) {
    return pako.deflate(content);
}

// 解压文件
function decompress(content) {
    try {
        return pako.inflate(content);
    } catch (e) {
        console.error("Error: Decompression failed. Please check your input.");
        throw e;
    }
}

// 加密文件
function encryptContent(content, key, isZip) {
    const compressed = isZip ? compress(content) : content;
    const encrypted = xxtea.encrypt(compressed, xxtea.toBytes(key));
    if (!encrypted) throw new Error("Encryption failed.");
    return encrypted;
}

// 解密文件
function decryptContent(content, key, isZip) {
    const decrypted = xxtea.decrypt(content, xxtea.toBytes(key));
    const decoded = isZip ? decompress(decrypted) : content;
    if (!decoded) throw new Error("Decryption failed. Check your key.");
    return decoded;
}

// 解密逻辑
function decrypt(isZip, key, jscPath) {
    console.log("Begin decrypt.");

    if (!fs.existsSync(jscPath)) {
        console.error("Error: The specified .jsc file does not exist.");
        return false;
    }

    const encryptedContent = readContentFromFile(jscPath);
    let decryptedContent;
    try {
        decryptedContent = decryptContent(encryptedContent, key, isZip);
    } catch (e) {
        console.error(e.message);
        return false;
    }

    const outputDir = "decryptOutput";
    if (!fs.existsSync(outputDir)) fs.mkdirSync(outputDir);

    const decryptedFilePath = path.join(outputDir, "dec.js");
    writeContentToFile(decryptedFilePath, decryptedContent);

    console.log("Decryption successful. Check the decryptOutput directory.");
    return true;
}

// 加密逻辑
function encrypt(isZip, key, jsPath) {
    console.log("Begin encrypt.");

    if (!fs.existsSync(jsPath)) {
        console.error("Error: The specified .js file does not exist.");
        return false;
    }

    const jsContent = readContentFromFile(jsPath);
    let encryptedContent;
    try {
        encryptedContent = encryptContent(jsContent, key, isZip);
    } catch (e) {
        console.error(e.message);
        return false;
    }

    const outputDir = "encryptOutput";
    if (!fs.existsSync(outputDir)) fs.mkdirSync(outputDir);

    const encryptedFilePath = path.join(outputDir, "projectChanged.jsc");
    writeContentToFile(encryptedFilePath, encryptedContent);

    console.log("Encryption successful. Check the encryptOutput directory.");
    return true;
}

// 主程序入口
const args = process.argv.slice(2);
const command = args[0];
const options = {
    path: args[1], nozip: args.includes("--nozip"), key: args[2] || "",
};

if (!command || !["encrypt", "decrypt"].includes(command)) {
    console.log("Usage: node decjsc.js <encrypt|decrypt> <path> <key> [--nozip]");
    process.exit(1);
}

// const isZip = !options.nozip;
// const key = options.key || readline.question('Please input your encrypt/decrypt key: ');
// const filePath = options.path || readline.question('Please input your .jsc/.js path: ');
//
// if (command === 'decrypt') {
//     decrypt(isZip, key, filePath);
// } else if (command === 'encrypt') {
//     encrypt(isZip, key, filePath);
// }
// 创建 readline 接口
// 主程序入口
(() => {
    const args = process.argv.slice(2);
    const command = args[0];
    const options = {
        path: args[1], nozip: args.includes("--nozip"), key: args[2] || "",
    };

    if (!command || !["encrypt", "decrypt"].includes(command)) {
        console.log("Usage: node decjsc.js <encrypt|decrypt> <path> <key> [--nozip]");
        process.exit(1);
    }

    const isZip = !options.nozip;
    // 使用 readline-sync 获取用户输入
    const key = options.key || readlineSync.question("请输入加密或解密的工程加密key 格式:xxxxxxxx-xxxx-xx: ");
    const filePath = options.path || readlineSync.question("请输入加密或者解密 .jsc/.js 的路径: ");

    if (command === "decrypt") {
        decrypt(isZip, key, filePath);
    } else if (command === "encrypt") {
        encrypt(isZip, key, filePath);
    }
})();
```

package.json

```json
{
  "name": "decjsc",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "description": "",
  "dependencies": {
    "pako": "^2.1.0",
    "readline-sync": "^1.4.10",
    "xxtea-node": "^1.1.5"
  }
}

```



## 使用示例

#### 加密JavaScript文件

```bash
# 命令行方式
node index.js encrypt path/to/your/file.js your-secret-key

# 交互式方式
node index.js encrypt
# 然后按提示输入密钥和文件路径
```

加密成功后，加密后的JSC文件将保存在`encryptOutput`目录下，文件名为`projectChanged.jsc`。

#### 解密JSC文件

```bash
# 命令行方式
node index.js decrypt path/to/your/file.jsc your-secret-key

# 交互式方式
node index.js decrypt
# 然后按提示输入密钥和文件路径
```

解密成功后，解密后的JS文件将保存在`decryptOutput`目录下，文件名为`dec.js`。

#### 不使用压缩的加密/解密

如果你不希望在加密过程中使用压缩（或在解密过程中进行解压缩），可以添加`--nozip`选项：

```bash
node index.js encrypt path/to/your/file.js your-secret-key --nozip
node index.js decrypt path/to/your/file.jsc your-secret-key --nozip
```

## 总结

本文介绍了一个基于Node.js的JSC文件加密解密工具，它使用XXTEA加密算法和Pako压缩库，能够有效地保护JavaScript代码。该工具支持命令行参数和交互式输入，使用简单方便。通过这个工具，开发者可以在保持JavaScript灵活性的同时，为代码增加一层保护，使得核心业务逻辑不易被窃取或篡改。

## 参考资料

- [XXTEA加密算法](https://github.com/xxtea/xxtea-nodejs)
- [Pako压缩库](https://github.com/nodeca/pako)