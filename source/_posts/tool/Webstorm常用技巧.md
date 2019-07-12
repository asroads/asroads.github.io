---
title: Webstorm常用技巧
date: 2019-06-15 20:59:27
categories: "tool"
tags:
- 技术
---

### Webstorm 常用的一些技巧

从2015年开始使用Webstorm有几年了，很多时候遇到问题总是去网上搜索，显得麻烦，顺便记录一些，常用的技巧，这样以后重装电脑系统了，换环境了，可以一并找到，不用每次遇到问题都重新搜索一遍。（持续更新）

<!-- more -->

1. WebStorm 重置 

   - 原因：有时候不小心点击了某个地方，或者执行了某些操作，回不到正常的编程环境
   - 解决办法：
     - 关闭WebStorm
     - cd ~/Library/Preferences/
     - rm -rf WebStormXX（我是直接找到这个路径去手动删除的）

2. JavaScript 跨域

   - 做游戏需要连接服务器调试，有时候访问需要跨域

   - 解决办法：

     	- 在Webstorm 设置的 Web Browsers 下 大家自己调试的浏览器 点击编辑 在命令行里输入

     ```
      --args --disable-web-security --user-data-dir
     ```

