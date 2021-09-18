---
title: Mac环境下配置不同的账号和邮箱信息
comments: true
categories: tool
tags:
  - git
abbrlink: d2d883a7
date: 2021-09-18 13:46:46
---

对于经常程序开发的人来说，有时候面对的是不同的`Git`仓库和信息，此时账号密码都不一样，有时候提交信息很不方便，经常会使用一个全局默认账号信息，到账我们提交的仓库没有权限，导致`push` 失败。同一台电脑往往只配置哟个`Git` 账号，只能`Push`一个网站，如果面对多个账号的时候很不方便，下面就说说如何切换账号信息。
<!--more-->

## 步骤概要

1. 查看当前电脑的`Git`全局配置
2. 查看当前`Git`账户和邮箱
3. 设置当前项目设置的名称和邮箱

## 步骤细节

#### 查看当前Git账户和邮箱：

```shell
git config --global user.name
git config --global user.email
```

#### 清除全局邮箱和全局名称

```shell
git config --global --unset user.name
git config --global --unset user.email
```

#### 针对每个项目进行单独的设置

```shell
git config user.email "XXX@XX.com"
git config user.name "XXX"
```

#### 查看当前项目设置的名称和邮箱

```shell
git config user.email
git config user.name
```

## 其他

### 文件大小写

#### 查看是否忽略大小写

```shell
git config core.ignorecase
```

  `true`为忽略大小写   `false`为不忽略大小写

#### 关闭git忽略大小写配置

```shell
git config core.ignorecase false
```

### 放弃本地修改 强制更新

```shell
git fetch --all
git reset --hard origin/master
```

`git fetch` 只是下载远程的库的内容，不做任何的合并 `git reset` 把`HEAD`指向刚刚下载的最新的版本,`master`为分支名字

### git报错HTTP Basic: Access denied

#### 具体错误如下:

```shell
remote: HTTP Basic: Access denied fatal: Authentication failed for "xxx你的远程仓库"
```

另外一种情况：

```shell
git Failed to connect to port xxxx: Connection refused
```

这两种情况, 很大程度是第一次使用的时候登的帐户和密码跟现用的仓库帐户密码对不上, 所以就提示用户无权限连接, 或者拒绝访问. 这时, 最好的方法是将`git`帐户密码重置!

#### 解决方法

进入到本地仓库目录下, 终端命令行里面 , 在弹出里输入如下指令

```shell
git config --system --unset credential.helper
```

重新操作`git`, 再次输入帐户和密码, OK, 成功!

## 参考

- [使用git报错 - remote: HTTP Basic: Access denied](https://blog.csdn.net/qq_25835645/article/details/84390221)
- [mac 下配置多个Git账户](https://www.jianshu.com/p/692a79beca0d)
- [修改文件名大小写造成的git上传文件丢失](https://my.oschina.net/huibaifa/blog/3096818)



