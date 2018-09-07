---
title: Mac 小技巧集锦
date: 2018-07-15 20:30:03
categories: "other"
tags:
- Mac
---

### 常用的快捷键

#### 1. CMD－Z：撤消文件的复制、移动或删除操作（CMD－Z）

CMD－Z 是一个深度整合的快捷键，你可以简单理解为「撤消」操作，无论是撤销文本输入或是撤销文件删除操作。按一下 CMD－Z，就会返回到上一状态。尤其是删除文件了，一次就可以复位，非常方便。

已经连续删除了好几个文件？移动了好几次文件？没有关系，你只需多按几次 CMD－Z，直到你发现完全恢复了以前的样子为止。

#### 2. Return（回车）键：重命名文件/文件夹

重命名文件/文件夹的最简单方法是在选择该项目后，按一下 Return 键（回车键）。这将即刻选中文件/文件夹的名称并呈现可编辑的状态。立刻开始输入新名称吧。完成命名了？再敲一次回车键即可。简单方便。

当然这不是 OS X 里重命名文件或文件夹的唯一方式，你还可以使用标题栏，或是命令行，或是使用鼠标点击文件名来实现。但是，哪个快？

#### 3. Space（空格）键：即时预览

Finder 内置了最为 handy 的即时预览功能（我个人最喜欢的 Finder 功能之一），一旦你会了，简直是离不开它。在 Finder 中选择任何一个项目，按下空格键。这将立即打开一个特殊窗口：Pdf 也好，音乐也好，视频也好，几乎什么格式都可以立即实时预览。再按一下空格键，就可立即关闭这个预览窗口。

### 显示 Finder 隐藏文件

喜欢捣鼓隐藏文件的用户看过来吧，我们可以让 Finder 始终显示隐藏文件或文件夹。

打开「终端」应用程序输入如下命令：

```
defaults write com.apple.finder AppleShowAllFiles -boolean true ; killall Finder
```

回车即可。现在你将会在 Finder 中看到隐藏的文件和文件夹了。

恢复默认：将上述命令换成下面这条即可。

```
defaults write com.apple.finder AppleShowAllFiles -boolean false ; killall Finder
```

当然 用快捷键 **shift+cmmand+.** ，接着看到隐藏文件夹内凡是前面带有小点的隐藏文件，或者是显示淡蓝色的文件都是隐藏文件。通过这个方式就可以查看隐藏的目录。要恢复隐藏文件的话再次按**shift+cmmand+.** ，即可恢复文件的隐藏状态，如图二隐藏的文件已经不可见。

### Mac 命令行下直接控制Wi-Fi提高效率

```
networksetup -listallhardwareports
```

一般 会输出

```
Hardware Port: Ethernet
Device: en0
Ethernet Address: a8:60:b6:2b:c8:19

Hardware Port: Wi-Fi
Device: en1
Ethernet Address: 58:40:4e:ee:cc:04

Hardware Port: Bluetooth PAN
Device: en4
Ethernet Address: 58:40:4e:ee:cc:05

Hardware Port: Thunderbolt 1
Device: en2
Ethernet Address: ea:00:00:e5:2d:80

Hardware Port: Thunderbolt 2
Device: en3
Ethernet Address: ea:00:00:e5:2d:81

Hardware Port: Thunderbolt Bridge
Device: bridge0
```

- 关闭 WiFi 

```
networksetup -setairportpower en1 off
```

- 开启WiFi

```
networksetup -setairportpower en1 on
```

