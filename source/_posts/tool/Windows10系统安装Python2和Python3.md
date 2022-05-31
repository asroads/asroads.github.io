---
title: Windows10系统安装Python2和Python3
comments: true
categories: tool
tags:
  - Python
abbrlink: 4779a2c4
date: 2022-05-30 19:34:37
---

最近从Mac换了windows10，开始配置一些常用的配置，其中就有常见的Python，下面就说是如何在Windows10环境下如何同时安装Python2.7和Python3.10。本文是在**Windows64位系统**下进行的，32位系统请下载相应版本的安装包，安装方法类似。使用python开发，环境有Python2和 python3 两种，有时候需要两种环境切换使用。

<!--more-->

## 运行环境

- Windows10

## 需要软件

- Python2.7.18
- Python3.10

## 步骤

### **下载python3和python2**

进入python官网，链接https://www.python.org/ 选择(Downloads )下载页面 https://www.python.org/downloads/ 选择Windows自己要安装的版本，这里选择[2.7.18](https://www.python.org/downloads/release/python-2718/)。

![image-20220530200427835](Windows10%E7%B3%BB%E7%BB%9F%E5%AE%89%E8%A3%85Python2%E5%92%8CPython3/image-20220530200427835.png)

![image-20220530200528269](Windows10%E7%B3%BB%E7%BB%9F%E5%AE%89%E8%A3%85Python2%E5%92%8CPython3/image-20220530200528269.png)

本教程先安装python3，然后安装python2。首先选择安装目录，本文安装路径为`D:\System\Python\Python310`，然后点击下载好的python3软件包进行安装，具体流程如下：

#### Python3安装

![image-20220530200907591](Windows10%E7%B3%BB%E7%BB%9F%E5%AE%89%E8%A3%85Python2%E5%92%8CPython3/image-20220530200907591.png)

直接点击Next即可

![image-20220530200955918](Windows10%E7%B3%BB%E7%BB%9F%E5%AE%89%E8%A3%85Python2%E5%92%8CPython3/image-20220530200955918.png)

![image-20220530201135743](Windows10%E7%B3%BB%E7%BB%9F%E5%AE%89%E8%A3%85Python2%E5%92%8CPython3/image-20220530201135743.png)

其中绿色标记为VS 2015及其以后版本需要的，表示以后打算在VS 2015中配置python环境请勾选此项

![image-20220530201244659](Windows10%E7%B3%BB%E7%BB%9F%E5%AE%89%E8%A3%85Python2%E5%92%8CPython3/image-20220530201244659.png)

等待安装结束，点击Close

![image-20220530201327284](Windows10%E7%B3%BB%E7%BB%9F%E5%AE%89%E8%A3%85Python2%E5%92%8CPython3/image-20220530201327284.png)

此时我们可以查看`python3`安装情况，按“Win + R”快捷键，在打开的“运行”对话框中，输入“cmd”按回车，进入如下界面，输入`python`查看python3安装情况，显示`python 3.10.4` 的版本信息，则安装成功，可以输入语句`print(1 + 1)`进行测试。

![image-20220530201537823](Windows10%E7%B3%BB%E7%BB%9F%E5%AE%89%E8%A3%85Python2%E5%92%8CPython3/image-20220530201537823.png)

如果是要进行Python开发 找到python3 IDLE安装位置，发送快捷方式到桌面即可

![image-20220530201720122](Windows10%E7%B3%BB%E7%BB%9F%E5%AE%89%E8%A3%85Python2%E5%92%8CPython3/image-20220530201720122.png)

#### Python2安装

![image-20220530201931935](Windows10%E7%B3%BB%E7%BB%9F%E5%AE%89%E8%A3%85Python2%E5%92%8CPython3/image-20220530201931935.png)

直接点击 Next

![image-20220530202052950](Windows10%E7%B3%BB%E7%BB%9F%E5%AE%89%E8%A3%85Python2%E5%92%8CPython3/image-20220530202052950.png)

修改我们自己定义的路径，接下来如果弹框 提升 选择 Yes

![image-20220530202148859](Windows10%E7%B3%BB%E7%BB%9F%E5%AE%89%E8%A3%85Python2%E5%92%8CPython3/image-20220530202148859.png)

![image-20220530202248783](Windows10%E7%B3%BB%E7%BB%9F%E5%AE%89%E8%A3%85Python2%E5%92%8CPython3/image-20220530202248783.png)

此时我们发现 这个 无法添加python路径，此时我们可以先忽略即可 继续点击Next即可。

![image-20220530202334387](Windows10%E7%B3%BB%E7%BB%9F%E5%AE%89%E8%A3%85Python2%E5%92%8CPython3/image-20220530202334387.png)

![image-20220530202349950](Windows10%E7%B3%BB%E7%BB%9F%E5%AE%89%E8%A3%85Python2%E5%92%8CPython3/image-20220530202349950.png)

安装结束 点击 Finish即可。此时Python3 和 Python2安装完毕。

**特别说明：此时运行“cmd”命令进入DOS命令提示框，输入python可能显示python3版本，也可能显示python2版本，原因很可能是环境变量中Path的前后顺序不一样的问题，但要确定4个不同的Path值都有（见下一步）**

```
D:\System\Python\Python27\
D:\System\Python\Python27\Scripts
D:\System\Python\Python310\
D:\System\Python\Python310\Scripts\
```

自己手动配置系统环境变量，此电脑右键选择属性---选择高级系统设置--高级--环境变量，点击编辑，新建，分别添加D:\System\Python\Python27\和D:\System\Python\Python27\Scripts到环境变量。

![image-20220530203005771](Windows10%E7%B3%BB%E7%BB%9F%E5%AE%89%E8%A3%85Python2%E5%92%8CPython3/image-20220530203005771.png)

![image-20220530203100195](Windows10%E7%B3%BB%E7%BB%9F%E5%AE%89%E8%A3%85Python2%E5%92%8CPython3/image-20220530203100195.png)

注意:python3安装时可以选择自动添加到系统环境变量，如未选择，方法和python2添加过程相同。

### 修改python.exe自定义名字

找到python2和python3的安装目录，修改python2.7.18和python3.10.4中python.exe和pythonw.exe的名称为python2.exe、pythonw2.exe和python3.exe、pythonw3.exe。

说明：我这里只修改python3的名字Python2保持不变。

## 检验结果

然后在运行cmd命令，输入python即可运行python2.7.18版本，输入python3 即可运行python3.10.4版本

![image-20220530203531430](Windows10%E7%B3%BB%E7%BB%9F%E5%AE%89%E8%A3%85Python2%E5%92%8CPython3/image-20220530203531430.png)

![image-20220530203549077](Windows10%E7%B3%BB%E7%BB%9F%E5%AE%89%E8%A3%85Python2%E5%92%8CPython3/image-20220530203549077.png)

## **pip2 和 pip3设置**

Python 安装包需要用到包管理工具pip，但是当同时安装python2和python3的时候，pip只是其中一个版本，以下将提供一个修改方式，即重新安装两个版本的pip，使得两个python版本的pip能够共存。

在DOS命令框输入命令，`python3 -m pip install --upgrade pip --force-reinstall`，显示重新安装成功。

![image-20220530203732060](Windows10%E7%B3%BB%E7%BB%9F%E5%AE%89%E8%A3%85Python2%E5%92%8CPython3/image-20220530203732060.png)

在DOS命令框输入命令，`python -m pip install --upgrade pip --force-reinstall`，显示重新安装成功。

![image-20220530203844899](Windows10%E7%B3%BB%E7%BB%9F%E5%AE%89%E8%A3%85Python2%E5%92%8CPython3/image-20220530203844899.png)

可以通过`pip2 -V` 和 `pip3-V` 查看两个版本的pip信息，以后只需运行`pip2 install XXX`和`pip3 install XXX`即可安装各自的python包。

![image-20220530203949836](Windows10%E7%B3%BB%E7%BB%9F%E5%AE%89%E8%A3%85Python2%E5%92%8CPython3/image-20220530203949836.png)

至此，pip2和pip3 也修改成功。

## 参考

- [Windows10下python3和python2同时安装（一）安装python3和python2](https://blog.csdn.net/qiang12qiang12/article/details/53239734)
