---
title: Windows环境下命令行工具Cmder常用设置
comments: true
categories: tool
tags:
  - Cmder
  - Windows
abbrlink: d9bfdc54
date: 2022-05-21 10:30:46
---

这里是你博客列表显示的摘要文字我们知道windows官方的命令行丑的要命，特别是习惯了Mac系统的程序员，用Windows系统的时候不免总是想找一个和[Item2](https://iterm2.com/downloads.html)类似的替代品，找寻了一大圈，发现[Cmder](https://cmder.net/)是个不错的产品，于是乎就拿来先顶着，工欲善其事必先利其器，下面就说一下一些常用比较好的设置。
<!--more-->
![Mmmm Monokai.](Windows%E7%8E%AF%E5%A2%83%E4%B8%8B%E5%91%BD%E4%BB%A4%E8%A1%8C%E5%B7%A5%E5%85%B7Cmder%E5%B8%B8%E7%94%A8%E8%AE%BE%E7%BD%AE/main.png)

## 官网下载

cmder是一个增强型命令行工具，不仅可以使用windows下的所有命令，更爽的是可以使用linux的命令,shell命令。下面是官网地址：https://cmder.net/

首先有两个版本 ，分别是mini与full版；唯一的差别在于有没有内建msysgit工具，这是Git for Windows的标准配备；全安装版 cmder 自带了 msysgit, 压缩包 23M, 除了 git 本身这个命令之外, 里面可以使用大量的 linux 命令

![image-20220531103748540](Windows%E7%8E%AF%E5%A2%83%E4%B8%8B%E5%91%BD%E4%BB%A4%E8%A1%8C%E5%B7%A5%E5%85%B7Cmder%E5%B8%B8%E7%94%A8%E8%AE%BE%E7%BD%AE/image-20220531103748540.png)

## 安装步骤

步骤比较简单，直接下载后解压到程序安装目录即可。

![image-20220531103943053](Windows%E7%8E%AF%E5%A2%83%E4%B8%8B%E5%91%BD%E4%BB%A4%E8%A1%8C%E5%B7%A5%E5%85%B7Cmder%E5%B8%B8%E7%94%A8%E8%AE%BE%E7%BD%AE/image-20220531103943053.png)

## 配置环境变量

在系统变量添加

- 变量名：`CMDER_HOME`
- 变量值： 安装绝对路径

![image-20220531104130244](Windows%E7%8E%AF%E5%A2%83%E4%B8%8B%E5%91%BD%E4%BB%A4%E8%A1%8C%E5%B7%A5%E5%85%B7Cmder%E5%B8%B8%E7%94%A8%E8%AE%BE%E7%BD%AE/image-20220531104130244.png)

接下来在Path新建一行路径`%CMDER_HOME%`

![image-20220531104251944](Windows%E7%8E%AF%E5%A2%83%E4%B8%8B%E5%91%BD%E4%BB%A4%E8%A1%8C%E5%B7%A5%E5%85%B7Cmder%E5%B8%B8%E7%94%A8%E8%AE%BE%E7%BD%AE/image-20220531104251944.png)

至此，系统环境变量已经添加完毕。

## 添加 cmder 到右键菜单

日常开发中在当前目录下打开命令行的需求太重要了，这个功能简直就是必须要的功能之一。配置环境变量后，在**管理员权限**的终端输入以下语句。Win8或者Win10可以直接 win+x 再按 a 键进入。

```bash
Cmder.exe /REGISTER ALL
```

![image-20220531104636564](Windows%E7%8E%AF%E5%A2%83%E4%B8%8B%E5%91%BD%E4%BB%A4%E8%A1%8C%E5%B7%A5%E5%85%B7Cmder%E5%B8%B8%E7%94%A8%E8%AE%BE%E7%BD%AE/image-20220531104636564.png)

配置好后，我们可以在任意文件夹右键

![image-20220531104738764](Windows%E7%8E%AF%E5%A2%83%E4%B8%8B%E5%91%BD%E4%BB%A4%E8%A1%8C%E5%B7%A5%E5%85%B7Cmder%E5%B8%B8%E7%94%A8%E8%AE%BE%E7%BD%AE/image-20220531104738764.png)

## 新标签打开个管理员权限终端

快捷键 Ctrl + t 后勾选

![image-20220531104905205](Windows%E7%8E%AF%E5%A2%83%E4%B8%8B%E5%91%BD%E4%BB%A4%E8%A1%8C%E5%B7%A5%E5%85%B7Cmder%E5%B8%B8%E7%94%A8%E8%AE%BE%E7%BD%AE/image-20220531104905205.png)

## 设置语言为中文

中国人开发嘛，菜单如果都是中文的更容易理解

![image-20220531105043453](Windows%E7%8E%AF%E5%A2%83%E4%B8%8B%E5%91%BD%E4%BB%A4%E8%A1%8C%E5%B7%A5%E5%85%B7Cmder%E5%B8%B8%E7%94%A8%E8%AE%BE%E7%BD%AE/image-20220531105043453.png)

![image-20220531105104078](Windows%E7%8E%AF%E5%A2%83%E4%B8%8B%E5%91%BD%E4%BB%A4%E8%A1%8C%E5%B7%A5%E5%85%B7Cmder%E5%B8%B8%E7%94%A8%E8%AE%BE%E7%BD%AE/image-20220531105104078.png)

## 解决中文乱码问题

我的系统是win10的，找到解决办法：
Settings->Startup->Environment 添加
set LANG=zh_CN.UTF-8
set LC_ALL=zh_CN.utf8

![image-20220531110545188](Windows%E7%8E%AF%E5%A2%83%E4%B8%8B%E5%91%BD%E4%BB%A4%E8%A1%8C%E5%B7%A5%E5%85%B7Cmder%E5%B8%B8%E7%94%A8%E8%AE%BE%E7%BD%AE/image-20220531110545188.png)

## **设置bash作为默认开启的选项**

![image-20220531105145783](Windows%E7%8E%AF%E5%A2%83%E4%B8%8B%E5%91%BD%E4%BB%A4%E8%A1%8C%E5%B7%A5%E5%85%B7Cmder%E5%B8%B8%E7%94%A8%E8%AE%BE%E7%BD%AE/image-20220531105145783.png)

## 更改背景和主题

![image-20220531105310501](Windows%E7%8E%AF%E5%A2%83%E4%B8%8B%E5%91%BD%E4%BB%A4%E8%A1%8C%E5%B7%A5%E5%85%B7Cmder%E5%B8%B8%E7%94%A8%E8%AE%BE%E7%BD%AE/image-20220531105310501.png)

## 常用功能介绍

1. 常用快捷键

- 利用Tab，自动路径补全；
- 利用Ctrl+T建立新页签；利用Ctrl+W关闭页签;
- 利用Ctrl+Tab切换页签;
- Alt+F4：关闭所有页签
- Alt+Shift+1：开启cmd.exe
- Alt+Shift+2：开启powershell.exe
- Alt+Shift+3：开启powershell.exe (系统管理员权限)
- Ctrl+1：快速切换到第1个页签
- Ctrl+n：快速切换到第n个页签( n值无上限)
- Alt + enter： 切换到全屏状态；
- Ctr+r 历史命令搜索
- **清屏** 
  - **方法一：输入cls,然后按下回车键**
  - **方法二：使用快捷键ctrl+L**


2, 可在视窗内搜寻画面上出现过的任意关键字。

3, 新增页签按钮。

4, 切换页签按钮。

5, 锁定视窗，让视窗无法再输入。

6, 切换视窗是否提供卷轴功能，启动时可查询之前显示过的内容。

7, 按下滑鼠左键可开启系统选单，滑鼠右键可开启工具选项视窗。 Win+Alt+P ：开启工具选项视窗。