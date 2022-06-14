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

#### 查看全局Git账户和邮箱：

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

#### remote: Permission to xxx/xxx.git denied to xxx

push的时候报错remote: Permission to xxx/xxx.git denied to xxx

问题出现环境：当你在命令行push一个项目的时候`git push origin master`出现此报错

##### 问题原因

翻译一下这个错误：用户<旧的github名>没有权限访问用户<新的github名>的存储库<项目名>。
就是说：现在电脑登录的是用户<旧的github名>，需要切换到用户<新的github名>。
那么，怎么切换账号呢，看操作

##### mac版解决方法

搜索钥匙串访问

![image-20211110102126160](Mac环境下配置不同的账号和邮箱信息/image-20211110102126160.png)

删除后 重新 `git push origin master` 即可。

### Sourcetree 删除账号密码

```bash
git config --system --unset credential.helper
```

如果没用 可以到sourcetree 的文件里面清除密码配置文件 修改用户配置文件

```bash
## windows路径
/username/admin/AppData/Local/Atlassian/SourceTree/passwd
/username/admin/AppData/Local/Atlassian/SourceTree/userhosts
## mac环境 用钥匙串解决
```

### SourceTree避免输入密码方法

##### 方式一：利用命令行

已经拉取完代码,在sourcetree 的界面打开终端:终端输入 

```bash
git config --global credential.helper store
```

##### 方式二：在git的congfig的url字段里加入账号密码

1. https://git.coding.net/项目.git

2. https://账号:密码@git.coding.net/项目.git

```bash
https://zhansan:123456@git.coding.net/test.git
```

### Windows版本

报错：`remote: Permission to xxx/xxx.git denied to xxx.`

解决：

找到 凭证管理器

![image-20220613144516845](Mac%E7%8E%AF%E5%A2%83%E4%B8%8B%E9%85%8D%E7%BD%AE%E4%B8%8D%E5%90%8C%E7%9A%84%E8%B4%A6%E5%8F%B7%E5%92%8C%E9%82%AE%E7%AE%B1%E4%BF%A1%E6%81%AF/image-20220613144516845.png)

找到 `github` 网站的凭证，删除 重新 `git push origin main`

![image-20220613144641741](Mac%E7%8E%AF%E5%A2%83%E4%B8%8B%E9%85%8D%E7%BD%AE%E4%B8%8D%E5%90%8C%E7%9A%84%E8%B4%A6%E5%8F%B7%E5%92%8C%E9%82%AE%E7%AE%B1%E4%BF%A1%E6%81%AF/image-20220613144641741.png)

   成功解决。

## 参考

- [使用git报错 - remote: HTTP Basic: Access denied](https://blog.csdn.net/qq_25835645/article/details/84390221)
- [mac 下配置多个Git账户](https://www.jianshu.com/p/692a79beca0d)
- [修改文件名大小写造成的git上传文件丢失](https://my.oschina.net/huibaifa/blog/3096818)
- [Sourcetree 删除账号密码](http://events.jianshu.io/p/2f63bb001f68)-简书
- [SourceTree避免输入密码方法](https://blog.csdn.net/jancywen/article/details/107232328)-CSDN



