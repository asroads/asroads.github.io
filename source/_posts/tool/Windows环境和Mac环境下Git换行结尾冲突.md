---
title: Windows环境和Mac环境下Git换行结尾冲突
comments: true
categories: tool
tags:
  - git
  - Windows
  - Cocos
abbrlink: e037b783
date: 2022-11-27 12:40:56
---

`CocosCreator`在Mac工作环境的开发的项目在Windows环境打开后`CocosCreator`生成的配置文件后缀为meta的文件全部都会冲突，在团队开发环境不同的情况下，会有很大困扰，如何解决呢？接下来就说说如何解决以及相关信息，并且加深拓展理解一些信息。
<!--more-->

## 具体步骤

### 问题描述

MAC OS下只有回车，windows下有回车换行，提交git时显示不一样。

### 问题分析

在不同操作系统中，换行符并不统一，Linux 系统中使用 `0x0A（LF）`, windows 系统中使用 `0x0D0A（CRLF）`, 而  MAC OS 系统起初使用`0x0D（CR）` 后来和 Linux 系统保持一致。而 git 默认采用 Linux 的换行符（当然这一点并不奇怪）。

git 为了解决不同平台换行符不一致的问题，在 windows 操作系统中默认在检出代码时将 `LF` 转换为 `CRLF`,而在提交的时候再转换为 `LF`，但是看似完美的解决方案在中文环境中却失效了。

#### git 全局参数

git 中有三个参数于换行符有关：

- `eol`: 设置工作目录中文件的换行符，有三个值 `LF`, `CRLF`和 `native`（默认，同操作系统）
- `autocrlf`:
  - `true` 表示检出是转换`CRLF`, 提交时转换为 `LF`
  - `input` 表示检出是不转换，提交时转换为 `LF`
  - `false` 表示不做转换
- `safecrlf`
  - `true` 表示不允许提交时包含不同换行符
  - `warn` 则只在有不同换行符时警告
  - `false` 则允许提价时有不同换行符存在

#### git设置配置项:

```bash
# 拒绝提交包含混合换行符的文件
git config --global core.safecrlf true

# 允许提交包含混合换行符的文件
git config --global core.safecrlf false

# 提交包含混合换行符的文件时给出警告
git config --global core.safecrlf warn

```

```bash
# 提交时转换为LF，检出时转换为CRLF
git config --global core.autocrlf true

# 提交时转换为LF，检出时不转换
git config --global core.autocrlf input

# 提交检出均不转换
git config --global core.autocrlf false
```

### 解决方法

#### 方案一

```bash
git config --global core.safecrlf true
git config --global core.autocrlf input

```

原因一：需要始终保证文件内容没有包含混合的换行符
原因二：这里服从多数情况，所以检出时我不转换，但提交要变成LF

#### 方案二

```bash
<!--统一换行符为 lf-->
git config --global core.eol lf
<!--将自动转换关闭,避免转换失败不能不同进行提交-->
git config --global core.autocrlf false
<!--禁止混用 lf 和 crlf 两种换行符-->
git config --global core.safecrlf true

```

## 加强团队配置

#### 增加配置文件 `.gitattributes`

虽然通过设置了 git 全局参数解决了问题，但是作为团队协作的话，并不能保证所有人都正确配好了。git 提供了`.gitattributes`文件解决了这个问题。在项目根目录新建`.gitattributes`文件，添加一下内容：

```bash
# Set the default behavior, in case people don't have core.autocrlf set.
* text eol=lf
```

通过这种方式避免有人没有设置 `core.autocrlf` 参数，并且将该文件加入版本控制中。

另外根据需要 `.gitattributes` 文件可以在项目不同目录中创建，而一些非文本文件可以设置为二进制文件，不用考虑换行符问题。

## 参考

- [git 设置不同平台行尾符号的问题](https://blog.csdn.net/u014298440/article/details/92659085) --CSDN
- [git的行尾的换行结束符处理](https://blog.51cto.com/u_15360362/3809418) --51CTO
- [git 多平台统一换行符](https://juejin.cn/post/6844903591258357773)--掘金
- [Git错误fatal: CRLF would be replaced by LF in xxx](https://www.jianshu.com/p/bbfe14787f39)

