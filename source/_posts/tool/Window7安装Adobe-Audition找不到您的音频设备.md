---
title: Window7安装Adobe Audition找不到您的音频设备
date: 2018-09-27 12:00:30
categories: "tool"
tags:
- 电脑
---

因为开发需要一些音效，自己就去网上找了一些免费的音效，这些音效有的部分可以用，就拿起了大学学习的专业课，于是乎，说干就干，安装了adobe audition cs3，只是这次与以往不同，出现了一个问题，“找不到支持的音频设备.....” 。

<!-- more -->

这个头大了，于是卸载重装了几次，换了几个软件都失败了，遇到问题总要解决吧，于是乎，还真被我找到了，大概是这样的，安装的时候，注册表里路径指示的不是我们软件的真实路径，修改一下就好了，是不是很简单的样子，于是下面开讲：

1. 首先按  win+r（ps：别告诉我你不知道神马是win键），输入regedit，打开注册表。

2. 按  ctrl+f  打开搜索对话框，在查找目标中输入`Audition asio.dll`（有可能是小写的“audition asio.dll”），慢慢等结果出来。 这个过程很漫长.

3. 最终结果，比如我的在注册表的这个位置：

   HKEY_CLASSES_ROOT\Wow6432Node\CLSID\{AB7EE7CD-AD07-4A27-B986-C7D5A31C8098}\InprocServer32

HKEY_CLASSES_ROOT\Wow6432Node\CLSID\{AB7EE7CD-AD07-4A27-B986-C7D5A31C8098}\InprocServer32

4. 修改默认的属性，比如我的软件装在这个位置D:\Program Files (x86)\Adobe\Adobe Audition 3.0\en_us\Audition asio.dll（注意根据自己的软件安装的位置来修改）替换原来的默认值。

最后重启软件，（注意：有的机子可能需要重启电脑），大功告成，不会在提示错误啦。。

**貌似这个方法在 Windows8 上面同样适用**希望能帮助遇到此问题的人。

