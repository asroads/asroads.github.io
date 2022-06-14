---
title: Windows10添加右键新建Markdown文件
comments: true
categories: tool
tags:
  - Markdown
  - Windows
abbrlink: c6e8081
date: 2022-03-14 16:06:52
---

最近使用`Windows10` 写工作文档的时候，发现右键没有新建`Markdown`文件（`.md`）于是乎就想看看网上有没有这样的教程，一搜索还真有，于是搬运过来，后面自己重装系统换了电脑等方便自己查找，下面简单的记录一下。
<!--more-->
本教程主要针对`Markdown`主要已经安装过[typora](https://typora.io/releases/all)编辑的用户，亲测好用。其他编辑器的用户请另外寻找方法。

## 步骤

1. 先去安装[typora](https://typora.io/releases/all) 下载免费版的0.11.18（当然如果你已经是付费用户，可以随意下载）
2. 随意新建后缀为`.txt`文件，复制以下内容修改后缀名为`.reg`双击运行，用完后删除即可。

```shell
Windows Registry Editor Version 5.00
 
[HKEY_CLASSES_ROOT\.md]
@="Typora.md"
"Content Type"="text/markdown"
"PerceivedType"="text"
 
[HKEY_CLASSES_ROOT\.md\ShellNew]
"NullFile"=""
```

3. 下面是运行后的测试效果

![image-20220614161414552](Windows10%E6%B7%BB%E5%8A%A0%E5%8F%B3%E9%94%AE%E6%96%B0%E5%BB%BAMarkdown%E6%96%87%E4%BB%B6/image-20220614161414552.png)

## 其他

此外介绍一款`Windows`环境好用的右键菜单管理工具 [ContextMenuManager](https://github.com/BluePointLilac/ContextMenuManager)  [前往下载](https://github.com/BluePointLilac/ContextMenuManager/releases)

## 参考

- [WIN10右键新建markdown](https://zhuanlan.zhihu.com/p/333800946)