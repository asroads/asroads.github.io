---
title: Mac电脑sourcetree导入git项目报错git status failed with code -1
comments: true
categories: tool
tags:
  - Mac
  - Sourcetree
  - git
abbrlink: 49b55b1c
date: 2023-02-27 10:15:50
---

这里是你博客列表显示的摘要文字，前几天帮前同事配置Mac电脑环境，在使用sourcetree 导入git项目的时候一直报错git status failed with code -1,重装软件还是如此，后面找到了原因，下面记录一下。
<!--more-->
Mac电脑是M1芯片，[Sourcetree](https://www.sourcetreeapp.com/)报错如下

```
git status failed with code -1
```

出现的原因：M1运行x86的需要安装Rosetta2转译

解决方法：

```
softwareupdate --install-rosetta
```

再输入

```
A
```

成功解决问题！