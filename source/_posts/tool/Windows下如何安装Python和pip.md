---
title: Windows下如何安装Python和pip
categories: tool
tags:
  - 技术
  - Python
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

### Windows 10 Python2.x 与Python3.x 兼容并存

> 前提：电脑上已经安装了Python 3.x
> 1 到Python 官网下载 Python 2.7
> https://www.python.org/
> 2 按照平时安装软件的方法，进行安装，注意在安装过程中， 选择安装目录
> 比如 C:\Python27 C:\Python36
> 3 待安装完成后，配置Python2.7的环境变量(此处略)。
> 4 上述3步都完成后，修改Python 2.7 与Python3.6.1的配置
> 1) 将C:\Python36 路径中的 python.exe 修改为python3.exe ，pythonw.exe修改为 pythonw3.exe
> 2) 将C:\Python27 路径中的python.exe 修改为python2.exe ,pythonw.exe修改为 pythonw2.exe
> 5 pip2 和 pip3设置
> Python 安装包需要用到包管理工具pip，但是当同时安装Python2.x和Python3.x的时候，pip只是其中一个版本，以下将提供一个修改方式，即重新安装两个版本的pip，使得两个python版本的pip能够共存。
> 在DOS命令框，进入到python安装路径，输入命令，python3 -m pip install --upgrade pip --force-reinstall，显示重新安装成功。
> 在DOS命令框，进入到python安装路径输入命令，python2 -m pip install --upgrade pip --force-reinstall，显示重新安装成功。
> 现在可以通过pip2 -V 和 pip3-V 查看两个版本的pip信息，以后只需运行pip2 install XXX和pip3 install XXX即可安装各自的python包。
> 至此，pip2和pip3 修改成功。

参考链接：[Windows 10 Python2.x 与Python3.x 兼容并存](https://blog.csdn.net/liujingqiu/article/details/86645559)

### 解决win10 cmd下运行python弹出windows应用商店

Windows 10 的五月更新为 Microsoft Store 应用商店带来了 Python 3.7

原因是这个环境变量“C:\Users\hongc\AppData\Local\Microsoft\WindowsApps”的优先级比我们创建的python环境变量优先级高

所以我们只需要删除这个环境变量即可

但是为了不影响正常功能

推荐将Python的环境变量放在前面即可

参考链接 [解决win10 cmd下运行python弹出windows应用商店](https://blog.csdn.net/u013421931/article/details/99736992)




