---
title: Mac系统禁止Chrome浏览器自动升级
comments: true
categories: other
tags:
  - 技术
  - Chrome
abbrlink: f372f9ef
date: 2025-04-22 22:58:44
---

在Mac系统下载了Chrome之后，正常情况下，随着Chrome官方推送我们会收到更新版本，这个设置本来是一个很好的用户体验，但是最近在工作中遇到了问题，就是Chrome升级最新版本后，发现Chrome中的Dev Tools 无法正常录制，进而无法检测项目的性能以及长任务和函数耗费时间，于是想到先退回以前的版本，然后禁用Chrome更新。
<!--more-->
首先我们需要下载Chrome的以往版本，我是从下面网址下载的。

## 步骤

https://google-chrome.cn.uptodown.com/mac/versions

![image-20250422155738360](Mac%E7%B3%BB%E7%BB%9F%E7%A6%81%E6%AD%A2Chrome%E6%B5%8F%E8%A7%88%E5%99%A8%E8%87%AA%E5%8A%A8%E5%8D%87%E7%BA%A7/image-20250422155738360.png)

### 选择下载的版本

这里完选择正常的版本：134.0.6998.118

https://google-chrome.cn.uptodown.com/mac/download/1063948513

![image-20250422154930023](Mac%E7%B3%BB%E7%BB%9F%E7%A6%81%E6%AD%A2Chrome%E6%B5%8F%E8%A7%88%E5%99%A8%E8%87%AA%E5%8A%A8%E5%8D%87%E7%BA%A7/image-20250422154930023.png)

### 断网安装版本

这里需要正常安装版本，但是要记得断网安装，否则我们一不小心点开Chrome，它会启动自动更新，这样会导致我们前功尽弃，所以这里要断网安装。

### 替换更新文件

1. 我们本地新建一个文件，名字叫`GoogleUpdater`，记得去掉后缀名

   ![image-20250422155323234](Mac%E7%B3%BB%E7%BB%9F%E7%A6%81%E6%AD%A2Chrome%E6%B5%8F%E8%A7%88%E5%99%A8%E8%87%AA%E5%8A%A8%E5%8D%87%E7%BA%A7/image-20250422155323234.png)

2. 然后找到 路径 `/Users/admin/Library/Application Support/Google`，其中`admin` 是我电脑的用户名，操作的时候，换成自己的对应路径。

3. 删除第二步找到路径下的 GoogleUpdater目录，然后使用第一步创建的文件替换即可，这样Chrome就无法自动更新了。

4. 如果想要恢复Chrome的更新，只需删除之前建立的文件 GoogleUpdater ，然后新建文件将其夹命名为 GoogleUpdater 即可。

### 验证禁用结果

此时，我们启动Chrome浏览器，电脑联网，然后打开Chrome 找到  `关于 Google Chrome` 发现Chrome 已经无法正常更新。

![image-20250422155841395](Mac%E7%B3%BB%E7%BB%9F%E7%A6%81%E6%AD%A2Chrome%E6%B5%8F%E8%A7%88%E5%99%A8%E8%87%AA%E5%8A%A8%E5%8D%87%E7%BA%A7/image-20250422155841395.png)

## 参考链接

- [Mac关闭和禁止Chrome自动更新方法附Mac Chrome老版本下载](https://www.macapp.so/tips/chrome-updates-off/)
