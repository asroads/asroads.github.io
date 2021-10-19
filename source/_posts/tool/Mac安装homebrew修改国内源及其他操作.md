---
title: Mac安装homebrew修改国内源及其他操作
comments: true
categories: tool
tags:
  - Mac
  - homebrew
abbrlink: 943b5d3c
date: 2021-09-18 14:48:59
---

`Mac`环境开发，[Homebrew](https://brew.sh/)是一个常用的包管理器，从软件安装、卸载，到更新、查看，所有功能一应俱全，因某些原因，国内安装的时候总是报错，使得开发路程变得异常艰辛，下面就说一下如何解决这些艰辛之路。主要从两个方面说一下解决方案。
<!--more-->

## 概要

![image-20210918145551542](Mac安装homebrew修改国内源及其他操作/image-20210918145551542.png)

官方网站：https://brew.sh/

Homebrew Packages  https://formulae.brew.sh/

```shell
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

安装  包 命令 ：

```shell
brew install wget
```

使用 [Homebrew Cask](https://formulae.brew.sh/cask/) 安装 macOS 应用程序、字体和插件以及其他非开源软件。

```shell
 brew install --cask firefox
```

如果运行报错 比如 403 等 错误 清除 缓存 再来试试

```shell
rm -rf /Users/$(whoami)/Library/Caches/Homebrew/

brew -v
```



## 切换国内源

Homebrew 主要分三部分 

1. brew.git、homebrew-core.git
2. homebrew-cask.git
3. homebrew-bottles

可以分别独立切换：

### 切换源

比如切换 为中国科大:`https://mirrors.ustc.edu.cn`

```bash
# 修改为中科大的
#第一步，替换 brew.git：
cd "$(brew --repo)"
git remote set-url origin https://mirrors.ustc.edu.cn/brew.git
#第二步，替换 homebrew-core.git：
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-core.git
brew update
```

### 切换homebrew-cask默认源

```bash
# 修改为中科大的
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-cask"
git remote set-url origin git://mirrors.ustc.edu.cn/homebrew-cask.git
```

### 切换 Homebrew-bottles

```bash
# 临时替换
export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles

# 永久替换
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles' >> ~/.bash_profile

source ~/.bash_profile
```

### 其他国内源

```bash
# 中国科大:
https://mirrors.ustc.edu.cn/brew.git
https://mirrors.ustc.edu.cn/homebrew-core.git
https://mirrors.ustc.edu.cn/homebrew-bottles

# 清华大学:
https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/brew.git
https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-core.git
https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles

# 阿里 不推荐 没有 homebrew-cask
https://mirrors.aliyun.com/homebrew/brew.git
https://mirrors.aliyun.com/homebrew/homebrew-core.git
https://mirrors.aliyun.com/homebrew/homebrew-bottles
```

## 重置官方源

```bash
# 重置 brew.git 为官方源
$ git -C "$(brew --repo)" remote set-url origin https://github.com/Homebrew/brew.git

# 重置 homebrew-core.git 为官方源
$ git -C "$(brew --repo homebrew/core)" remote set-url origin https://github.com/Homebrew/homebrew-core.git

# 重置 homebrew-cask.git 为官方源
$ git -C "$(brew --repo homebrew/cask)" remote set-url origin https://github.com/Homebrew/homebrew-cask

# zsh 注释掉 HOMEBREW_BOTTLE_DOMAIN 配置
$ vi ~/.zshrc
# export HOMEBREW_BOTTLE_DOMAIN=xxxxxxxxx

# bash 注释掉 HOMEBREW_BOTTLE_DOMAIN 配置
$ vi ~/.bash_profile
# export HOMEBREW_BOTTLE_DOMAIN=xxxxxxxxx

# 刷新源
$ brew update

```

## 比较简单好用的方式

#### Homebrew国内源

https://gitee.com/cunkai/HomebrewCN

> 苹果电脑标准安装脚本：（推荐 优点全面 缺点慢一点）
>
> ```
> /bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh)"
> ```
>
> 苹果电脑极速安装脚本：（优点安装速度快 缺点update功能需要命令修复 ）
>
> ```
> /bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh)" speed
> ```
>
> Linux 标准安装脚本：
>
> ```
> rm Homebrew.sh ; wget https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh ; bash Homebrew.sh
> ```
>
> 苹果电脑卸载脚本：
>
> ```
> /bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/HomebrewUninstall.sh)"
> ```
>
> Linux卸载脚本：
>
> ```
> rm HomebrewUninstall.sh ; wget https://gitee.com/cunkai/HomebrewCN/raw/master/HomebrewUninstall.sh ; bash HomebrewUninstall.sh
> ```
>
> **---Brew介绍**
>
> macOS 和 Linux 缺失软件包的管理器
>
> **---Homebrew 能干什么?**
>
> 使用 Homebrew 安装 Mac（或Linux）没有预装但你需要的东西。
>
> **--Homebrew自身如何使用**
>
> 知道软件包具体名称，直接 `brew install 软件包名` 只知道一小部分名称，用 `brew search 小部分名称` 查询即可 例如`brew search chrome`就会把带chrome的软件包全部列出
>
> **--Homebrew中的扩展cask如何使用**
>
> 假设安装firefox运行：
>
> ```bash
> brew install --cask firefox
> ```
>
> cask的图形化软件一般国内没有任何缓冲，下载很慢。

## 其他知识

### 区分Mac哪种终端工具

如果用的是 bash

```bash
>> ~/.bash_profile
source ~/.bash_profile
```

如果是zsh

```bash
>> ~/.zshrc
source ~/.zshrc
```



## 参考

- [Mac 安装 brew 修改国内源](https://www.jianshu.com/p/3556e2529e3e)

