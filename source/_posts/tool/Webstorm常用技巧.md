---
title: Webstorm常用技巧
categories: tool
tags:
  - 技术
  - Webstorm
abbrlink: 572cb29c
date: 2019-06-15 20:59:27
---

### Webstorm 常用的一些技巧

从2015年开始使用Webstorm有几年了，很多时候遇到问题总是去网上搜索，显得麻烦，顺便记录一些，常用的技巧，这样以后重装电脑系统了，换环境了，可以一并找到，不用每次遇到问题都重新搜索一遍。（持续更新）

<!-- more -->

### Mac彻底删除WebStorm 

#### 背景原因：

有时候不小心点击了某个地方，或者执行了某些操作，回不到正常的编程环境,或者错误设置了某些选项回不到最初的设置了

#### 步骤:

###### 一. 删除程序应用

1. 点击Finder
2. 选择左侧栏中的**Applcations**选项
3. 找到webstrom应用，点击右键，选择**Move to Trash(移入到垃圾箱)**
4. 将垃圾箱清空

###### 二. 删除目录下的App文件夹

1. 打开终端
2. **cd ~/Library/Preferences/**
3. ls 查看该目录下所有App文件夹,找到webstrom的文件夹(我的是WebStrom2018.3)
4. rm -rf WebStrom2018.3
5. **cd ~/Library/Caches/**
6. ls 查看该目录下所有App文件夹,找到webstrom的文件夹(我的是WebStrom2018.3)
7. rm -rf WebStrom2018.3
8. **cd ~/Library/Application Support/**
9. ls 查看该目录下所有App文件夹,找到webstrom的文件夹(我的是WebStrom2018.3)
10. rm -rf WebStrom2018.3
11. **~/Library/Logs/**
12. ls 查看该目录下所有App文件夹,找到webstrom的文件夹(我的是WebStrom2018.3)
13. rm -rf WebStrom2018.3
14. 当前已完全卸载了
     注：如果命令不能进入目录，利用Command + Shift + G 快捷键跳转到某个目录，以此删除即可
15. 原文链接：https://juejin.im/post/5ce20342e51d4510bf1d65d2

**注意：如果是最新版的 Webstorm 需要删除 的是 `jetbrains` 这个名字开头 或者含有 jetbrains 的文件夹 即可**

**~/Library/Application Support/**  有两个文件夹

![image-20200616145210704](Webstorm常用技巧/image-20200618100713237.png)

**记得 重置后 要 重置缓存 必要的话 **

- 删除项目下面的 .idea 文件夹
- Invalidate Cache/Restart

![image-20200616145210704](Webstorm常用技巧/image-20200616145210704.png)

### JavaScript 项目跨域

#### 详细步骤

- 做游戏需要连接服务器调试，有时候访问需要跨域

- 解决办法：

  	- 在Webstorm 设置的 Web Browsers 下 大家自己调试的浏览器 点击编辑 在命令行里输入

  ```bash
  
  --args --disable-web-security --user-data-dir
  ```
  
  ![image-20200529130131982](Webstorm常用技巧/image-20200529130131982.png)

顺便推荐一款Chome 清缓存和本地数据的插件：

#### Clear Cache

![image-20200529130729764](Webstorm常用技巧/image-20200529130729764.png)

地址：https://chrome.google.com/webstore/detail/clear-cache/cppjkneekbjaeellbfkmgnhonkkjfpdn

![image-20200529130754454](Webstorm常用技巧/image-20200529130754454.png)

选择上面两项即可。

### 忽略隐藏指定类型的文件指定文件(如.meta)不显示

Cocos 项目 在Webstorm 里面打开后 每个文件都佩带一个 .meta 文件，想忽略不显示

![image-20190903185643229](Webstorm常用技巧/image-20190903185643229.png)

忽略文件加入:

```javascript
*.meta;
```

以上即可。

### 搜索忽略指定的文件夹

有时候项目内很多文件和我们研发调试代码无关，比如素材或者派生文件，此时我们需要忽略这些文件，这样在查找问题的生活就极大的缩小了范围。

![image-20200529131313612](Webstorm常用技巧/image-20200529131313612.png)

同理 想要取消忽略 再次选择 设置即可

![image-20200529131343973](Webstorm常用技巧/image-20200529131343973.png)

### Webstorm Consloe 面板 中文不能输出

有时候我们安装了新的版本 Webstorm 后发现 控制面板 不能输出 中文

![image-20200529131627900](Webstorm常用技巧/image-20200529131627900.png)

结果这样显示：

![image-20200529131846232](Webstorm常用技巧/image-20200529131846232.png)

先说说 网上搜索的方法（经查看设置后 问题依然存在）

首先 采用 本地安装目录中bin文件夹下的webstorm.vmoptions   -Dfile.encoding=UTF-8
![image-20200529132117496](Webstorm常用技巧/image-20200529132117496.png)

File>Setting>Editor>File Encodings，将Global Encoding、Project Encoding、Default encodeing for properties files这三项都设置成UTF-8，点击OK或者Apply

![image-20200529132227654](Webstorm常用技巧/image-20200529132227654.png)

问题依然存在，怎么办，自己摸索了很久。终于找到原因，就是字体问题。

我们设置字体（能显示中文的字体）

![image-20200529133841482](Webstorm常用技巧/image-20200529133841482.png)

最后成功了！

![image-20200529133857201](Webstorm常用技巧/image-20200529133857201.png)

最后 重启 Webstorm 即可解决乱码问题。(注意 一定是完全退出，不是关闭项目打开 是退出Webstorm 重新打开)

