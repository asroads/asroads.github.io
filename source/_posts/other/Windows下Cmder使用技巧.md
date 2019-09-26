---
title: Windows下Cmder使用技巧
categories: other
tags:
  - Windows
abbrlink: fcab5eed
date: 2019-05-02 00:08:06
---

 这几天在住的地方学习的时候，需要一个可以快速学习的工作环境，于是乎，又回到了Windows 环境下编程学习，之前好多事情都是在Mac下面做的，现在回归Windows了，又要拾起很多以前的工具，毕竟好几年不怎么用Windows环境编程了，下面说说一个Windows下面比较好用的命令行工具 Cmder。

<!-- more -->

###  软件介绍

- 官网地址 <https://cmder.net/>

`cmder` 是一个跨平台的命令行增强工具，可以集成`windows batch`, `power shell`, `git`, `linux bash`等多种命令行于一体

其中官网有两个版本  一个是 迷你版本 Mini 版  一个是全功能版本 根据自己需要下载即可

### 软件技巧

- 快捷键

`ctrl` + `t`：快速新建一个 `tab`框；
`ctrl` + `w`：快速关闭一个 `tab`框；
`alt` + `enter`：全屏；
`ctrl` + `：快速打开或关闭 `cmder` 面板；
`shift` + 鼠标左键拖动：复制选择文本；
`win` + `alt` + `p` 键快速打开 `settings`页面；

**注意** 没有找到清屏的快捷键，用惯了item2 的我 只能用命令  “cls” 后 回车 进行清屏操作

- 添加 cmder 到右键菜单

  - 可以把Cmder.exe存放的目录添加到系统环境变量
  - 输入 Ctrl + t, 或者点击下面控制条的绿色加号, 勾选 Run as administrator，打开一个管理员权限终端
  - 在某个文件夹中打开终端, 这个是一个(超级)痛点需求, 实际上上一步的把 cmder 加到环境变量就是为此服务的, 在管理员权限的终端输入以下语句即可:

  ```shell
  	Cmder.exe /REGISTER ALL
  ```

- 私人定制

输入`win + alt + p`或者 在底部右击点击 settings, 进入设置页面；可以根据自己的所需进行各种配置(字体，字号，鼠标样式等等设置自己喜欢的风格)。

