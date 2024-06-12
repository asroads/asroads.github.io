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

## 解决 git status failed with code -1

Mac电脑是M1芯片，[Sourcetree](https://www.sourcetreeapp.com/)报错如下

```shell
git status failed with code -1
```

出现的原因：M1运行x86的需要安装Rosetta2转译

解决方法：

```shell
softwareupdate --install-rosetta
```

再输入

```shell
A
```

成功解决问题！

## 分步克隆大仓库 2024-06-12 更新 

对于特别大的仓库，可以尝试分步克隆，先克隆较少的历史记录，然后再获取更多的历史记录：

```shell
git clone --depth 1 https://e.coding.net/xxx/xxx/xxx-client.git
cd snake-client
git fetch --unshallow

```

设置 Git 超时时间

```shell
git config --global http.lowSpeedLimit 0
git config --global http.lowSpeedTime 999999

```

增加 Git 缓冲区大小：

```shell
git config --global http.postBuffer 524288000
```

#### 拿到远程所有的tag 

```shell
git -c diff.mnemonicprefix=false -c core.quotepath=false --no-optional-locks fetch --prune --tags origin
```

#### 拿到远程所有的branch

```shell
git config remote.origin.fetch +refs/heads/*:refs/remotes/origin/*
```

这个配置项告诉 Git 在执行 `git fetch` 时，从远程仓库 `origin` 获取所有的分支（`refs/heads/*`），并将它们映射到本地的远程跟踪分支（`refs/remotes/origin/*`）。配置完成后，每次执行 `git fetch`，Git 会自动获取所有的远程分支，并更新本地的远程跟踪分支。