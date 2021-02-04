---
title: Flash Player 被浏览器禁用后如何打开swf文件
comments: true
categories: other
tags:
  - 技术
  - Mac
abbrlink: 6a3151b2
date: 2021-02-04 12:04:33
---

2021新年伊始，曾经风靡全球的Flash Player开始被各大浏览器禁用，作为一个曾经的Flash Action Script 3.0 的开发者，在感慨万千的同时，想到更多的是接下来如何去解决浏览器不支持SWF文件后的解决方案（尽管最好的方案是避免使用SWF）但是事实却并不能完全的避免，毕竟这个曾经风靡全球的技术，还是带来了一些很好用的软件和动画的，下面我就说一下如何解决这些问题。

<!--more-->
其实平时我们用Flash SWF文件大部分时间都是用来播放，当然SWF是支持用户交互的，接下来我们就说说如何在Mac环境优雅的处理这些问题。

### 仅仅是播放

如果仅仅是解决播放问题，那么下面的这款软件可以说是做的相当优秀，话不多说直接上链接：

![image-20210204121705953](Flash-Player-被浏览器禁用后如何打开swf文件/image-20210204121705953.png)

官网地址：[Elmedia Player](https://mac.eltima.com/freeflashplayer.html#top)

下载地址：[SWF & FLV Player for Mac Advanced free Flash Player Mac](https://mac.eltima.com/elmedia-player-download.html)

![image-20210204121730723](Flash-Player-被浏览器禁用后如何打开swf文件/image-20210204121730723.png)

### 如果用来交互

其实上面的这个软件 也可以交互，比如鼠标键盘玩游戏，但是有时候涉及到输入的时候，比如汉字这样的操作就失灵了，此时我们只能另寻出路。下面就是一条路：

![image-20210204121747784](Flash-Player-被浏览器禁用后如何打开swf文件/image-20210204121747784.png)

Adobe Flash Player 有独立运行版，不依赖浏览器可以单独打开。点击网址 https://www.adobe.com/support/flashplayer/debug_downloads.html ，找到适合自己操作系统的版本下载即可。

[Adobe Flash Player Support Center 点击打开](https://www.adobe.com/support/flashplayer/debug_downloads.html)

> **Adobe Flash Player 32 (Win, Mac & Linux) standalone (aka projectors) players for Flex and Flash developers.**
>
> **Windows**
>
> - [![Download](Flash-Player-被浏览器禁用后如何打开swf文件/download.gif)Download the Flash Player projector content debugger](https://fpdownload.macromedia.com/pub/flashplayer/updaters/32/flashplayer_32_sa_debug.exe)
> - [![Download](Flash-Player-被浏览器禁用后如何打开swf文件/download.gif)Download the Flash Player projector](https://fpdownload.macromedia.com/pub/flashplayer/updaters/32/flashplayer_32_sa.exe)
>
> **Macintosh**
>
> - [![Download](Flash-Player-被浏览器禁用后如何打开swf文件/download.gif)Download the Flash Player projector content debugger](https://fpdownload.macromedia.com/pub/flashplayer/updaters/32/flashplayer_32_sa_debug.dmg)
> - [![Download](Flash-Player-被浏览器禁用后如何打开swf文件/download.gif)Download the Flash Player projector](https://fpdownload.macromedia.com/pub/flashplayer/updaters/32/flashplayer_32_sa.dmg)
>
> **Linux**
>
> - [![Download](Flash-Player-被浏览器禁用后如何打开swf文件/download.gif)Download the Flash Player Projector (64-bit)](https://fpdownload.macromedia.com/pub/flashplayer/updaters/32/flash_player_sa_linux.x86_64.tar.gz)
> - [![Download](Flash-Player-被浏览器禁用后如何打开swf文件/download.gif)Download the Flash Player Projector content debugger (64-bit)](https://fpdownload.macromedia.com/pub/flashplayer/updaters/32/flash_player_sa_linux_debug.x86_64.tar.gz)
>
> #### PlayerGlobal (.swc)
>
> - [![Download](Flash-Player-被浏览器禁用后如何打开swf文件/download.gif)Download the playerglobal.swc to target the latest version APIs](https://fpdownload.macromedia.com/get/flashplayer/updaters/32/playerglobal32_0.swc)

### 字体编辑器 

Littera 是一款很优秀的位图字体编辑器，可以在线编辑生成位图字体，其网站主要工具就是一个swf 文件

Flash 被浏览器禁用后，如果想继续使用无非是选择可以支持的浏览器或者找一个播放软件，那么官方的这个 独立版本的软件上自然是一个最佳选择，当然前提是你下载了这个网站的swf 文件 

这里提前下载了一个离线版本 [Littera-bitmap-font-generator 本地版本](https://github.com/jsroads/Littera-bitmap-font-generator)

![image-20210204125432022](Flash-Player-被浏览器禁用后如何打开swf文件/image-20210204125432022.png)

