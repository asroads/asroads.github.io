---
title: Windows下如何安装Python和pip
categories: tool
tags:
  - Layabox
  - 技术
abbrlink: ba6692cc
date: 2018-09-13 12:11:18
---

因为开发需要帮助同事电脑安装Python环境，因为我这两年开发一直用的是Mac电脑所以，在Windows上面如何安装Python和pip 这个确实还没有遇到过，遇到问题，上网查找答案，然后自己照做，遇到问题了，反思是件好事，于是我就按照网上的几个办法，一路下来，还真成功了！

<!-- more -->

解决办法如下：

1. 安装python2.7.13，这里我选择安装在C盘根目录下。

2. 安装完毕后C盘会生成一个叫“python27”的文件夹。

3. 打开python27，会发现该目录下存在一个叫Scripts的文件夹，点开Scripts，会发现里面有一系列和easy_install有关的文件。

4. 打开python27，会发现该文件下有一个叫python.exe的文件，将该文件的路径添加至环境变量PATH（在系统变量一栏下添加）。如果不做这一步，则cmd命令是无法识别“python”指令的。能不能成功识别该指令分别会出现如下状况：

   a.给cmd输入“python”，不能成功识别：控制台显示“不是内部或外部命令”

   b.给cmd输入“python”，能成功识别，则显示一些关于python的 命令参数。

5. 现在cmd下就可以使用“python”指令了，一路cd到easy_install.exe的根目录下。

6. 这里需要强调一点，cmd默认路径是从“C:\Users\XXX>”开始的，如果想转到D盘E盘F盘方便，以E盘为例，直接输入“E:”就可以了.

7. 一路cd索引到easy_install.exe所在的地方，执行指令“easy_install.exe pip”

8. 则Scripts文件夹下会出现一系列和pip有关的文件，其中有pip.exe。

9. 和之前处理python.exe的方法一样，将pip.exe的路径(我的是C:\Python27\Scripts\pip.exe;)也添加到环境变量PATH中。

10. 重新打开控制窗口（r+win），cmd下输入“pip”，如果能识别"pip"指令，则说明pip安装成功了。

    也就是说会出现关于pip的参数。


最后，大功告成！！！参考文章：[windows下Python三步安装pip](https://www.cnblogs.com/babyfei/p/7079983.html)

