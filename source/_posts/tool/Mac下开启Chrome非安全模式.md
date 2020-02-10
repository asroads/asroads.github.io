---
title: Mac下开启Chrome非安全模式
categories: tool
tags:
  - Mac
  - 技术
abbrlink: 6b229175
date: 2019-04-12 19:46:37
---

以前开发游戏的时候都是单机游戏呀，现在开发有服务器的游戏。调试的时候，就发现了跨域的问题。非安全模式要才可以，但是很网上给的例子，当时一试运行，每次都带打开命令有点麻烦。                                                                                                                                                                       

<!-- more -->

```bash
# Chrome < 48
open /Applications/Google\ Chrome.app --args -disable-web-security

# Chrom > 48
open -a Google\ Chrome --args --disable-web-security --user-data-dir
```

## webstorm  打开Chome 非安全模式

- 打开 webstorm

  ![image-20190412193030932](Mac下开启Chrome非安全模式/image-20190412193030932.png)

- 设置 如下 

![image-20190412193122773](Mac下开启Chrome非安全模式/image-20190412193122773.png)



内容是：

```
--args --disable-web-security --user-data-dir 
```

然后重启Chome  就搞定了



### 附上 windows 和Mac 简易 操作

#### windows

第一步：确保所有chrome浏览器都已经关闭
第二步：打开你的chrome浏览器（快捷方式）的属性窗口，再路径后面添加

```shell
--args --disable-web-security --user-data-dir
```

效果如下：

```
C:\Program Files (x86)\Google\Chrome\Application\chrome.exe" --args --disable-web-security --user-data-dir
```

![image-20200205203455563](Mac下开启Chrome非安全模式/image-20200205203455563.png)

第三步：打开浏览器，如果出现这个横幅，就说明非安全模式开启完成。

![image-20200205203615488](Mac下开启Chrome非安全模式/image-20200205203615488.png)

#### MAC 解除chrome安全模式

打开终端输入



```kotlin
open -a /Applications/Google\ Chrome.app --args --disable-web-security --user-data-dir
```

OK  输入地址即可 。



## 参考文章

- [Mac下开启Chrome非安全模式](https://github.com/callmelaoda/communicate/issues/8)
- [Mac上解决Chrome浏览器跨域问题](https://www.jianshu.com/p/2db73311fcbe)
- [WebStorm中配置浏览器方法](https://blog.csdn.net/fd214333890/article/details/39401677)
- [chrome 非安全模式解决开发跨域问题(windows与mas)](https://www.jianshu.com/p/935a37d9af8b)