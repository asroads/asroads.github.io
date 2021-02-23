---
title: 取消Git子模块submodule
comments: true
categories: tool
tags:
- git
abbrlink: '535121e9'
date: 2021-02-22 17:56:15
---

有时候我们因项目需求，需要添加或者取消git 的子模块 此时需要如何操作呢？下面简单的说一下操作技巧。
<!--more-->

### git删除子模块

如果只需要将子模块代码放入主存储库，则只需删除子模块，然后将文件重新添加到主存储库中即可：

```shell
git rm --cached submodule_path # delete reference to submodule HEAD (no trailing slash)
git rm .gitmodules             # if you have more than one submodules,
                               # you need to edit this file instead of deleting!
rm -rf submodule_path/.git     # make sure you have backup!!
git add submodule_path         # will add files instead of commit reference
git commit -m "remove submodule"
```

### git pull 碰到拒绝合并无关历史

```bash
fatal: refusing to merge unrelated histories  // 拒绝合并无关历史
```

### 解决办法

在拉取时使用以下命令：

```bash
git pull origin master --allow-unrelated-histories 
```

对此，git 的 [官方文档](https://git-scm.com/docs) 是这样描述的：

> By default, git merge command refuses to merge histories that do not share a common ancestor. This option can be used to override this safety when merging histories of two projects that started their lives independently. As that is a very rare occasion, no configuration variable to enable this by default exists and will not be added.
>
> 默认情况下，git合并命令拒绝合并没有共同祖先的历史。当两个项目的历史独立地开始时，这个选项可以被用来覆盖这个安全。由于这是一个非常少见的情况，因此没有默认存在的配置变量，也不会添加。（有道翻译）

### 关SSL_ERROR_SYSCALL in connection to github.com:443错误的两种解决方案

### 错误来源

使用使用hexo部署博客是，遇到以下错误:

```
fatal: unable to access 'https://github.com/silenceboychen/silenceboychen.github.io.git/': LibreSSL SSL_connect: SSL_ERROR_SYSCALL in connection to github.com:443
FATAL Something's wrong. Maybe you can find the solution here: http://hexo.io/docs/troubleshooting.html
Error: Spawn failed
    at ChildProcess.<anonymous> (/Users/chenhao/new_start/nodejs/blog/node_modules/hexo-util/lib/spawn.js:52:19)
    at ChildProcess.emit (events.js:182:13)
    at Process.ChildProcess._handle.onexit (internal/child_process.js:239:12)
```

### 两种解决方案：

#### 方案一

取消http代理：

```
$ git config --global --unset http.proxy
$ git config --global --unset https.proxy
```

设置env `GIT_SSL_NO_VERIFY`为`true`然后再次部署：

```
$ env GIT_SSL_NO_VERIFY=true hexo d
```

问题解决。

#### 方案二

在hexo项目的根目录下的_config.yml文件中把仓库链接地址由https修改为ssh的地址。