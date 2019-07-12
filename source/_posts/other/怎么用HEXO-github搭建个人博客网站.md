---
title: 怎么用HEXO+github搭建个人博客网站
date: 2018-06-05 11:12:37
categories: "other"
tags:
- hexo
- 技术
---

## 建站前的一些唠叨

个生活的瞬间，总想着把自己的所想所感，所悟、所得记录下，哪怕是零星的，粗糙的，鄙俗的，都是自己对生活真相的一次次靠近的证明，于是催生了做个自己的博客的想法，2015年的使用了[WordPress](https://cn.wordpress.org/)技术做了一个博客，自己买了云服务器，域名，终于有了自己的个人网站，后来服务器到期前的那段时间，正赶上项目上线，没有来得及迁移，就被付之一炬。

<!-- more -->

前天[seazhang](https://seazhang.github.io/)童鞋发来几个建站教程，又一次萌生了开博客的想法，说干就干，栽种一棵树最好的时间是十年前或现在。于是乎，今年又开始了新的征程，活着，就要学习，2005 年乔布斯在斯坦福的演讲中说到“stay hungry stay foolish”，对世界怀有敬畏，好奇之心，才能保持自己活的永远年轻，永远热泪盈眶。

## 建站准备

我自己建站的环境是Mac环境，下面主要以Mac环境为准：

环境：

- Mac电脑，联网状态
- [Git](https://www.git-scm.com/download/) 、[Node.js(npm)](https://nodejs.org/zh-cn/)
- [GitHub账号](https://github.com/)

## 安装

- node.js用来创建hexo博客框架的用的依赖，我安装的是node-v8.11.3；
- Git环境，你可以安装客户端或者 用[Homebrew](https://brew.sh/) 安装git，关于[Homebrew](https://brew.sh/)的安装不在本教程讨论之内，大家自行搜索。

### 安装hexo

利用npm命令就可以安装[hexo](https://hexo.io/zh-cn/docs/)如果自己npm下载出错 可以用国内的一些网站镜像代替

比如[淘宝 NPM 镜像](http://npm.taobao.org/) 可以参考这篇文档 [国内优秀npm镜像推荐及使用](http://riny.net/2014/cnpm/)

```
npm install -g hexo
```

如果 遇到权限报错 可以 在前面加上 “sudo” 这个时候 会让你输入电脑密码获得权限，以下所有的指令，如果遇到问题都可以在前面加上“sudo” 尝试解决。

### 初始化

安装完毕，选择本地你要建站的文件夹，我的是 “/Users/hoolai/jsroads/myblog”，然后打开控制台执行命令，hexo 会初始化网站所需的文件。

```
hexo init
```

接着安装 npm 依赖包

```
npm install
```

上面的一切就绪之后，就可以开始本地测试一下了。关于hexo 指令可以参考 [hexo指令](https://hexo.io/zh-cn/docs/commands.html) 运行生成静态文件的指令

“hexo generate” 你也可以使用 “hexo g”代替

```
hexo generate
```

编译成功后 继续输入 “hexo server” 部署网站 可以用“hexo s” 代替

```
hexo server
```

此时 可以用浏览器访问 [http://localhost:4000，查看一下默认的效果](http://localhost:4000%EF%BC%8C%E6%9F%A5%E7%9C%8B%E4%B8%80%E4%B8%8B%E9%BB%98%E8%AE%A4%E7%9A%84%E6%95%88%E6%9E%9C/) 此时网站还是只能本地看到，默认主题是landscape。

## 部署到GitHub上

首先你要有一个GitHub账号，这个很简单，如果没有，去注册一个就好。

### 创建一个repository

repository 相当于一个仓库，用来放置自己的网站的文件，登录进 GitHub 页面 选择 repositories 然后 创建一个新的 repository 这个时候很简单，写一个Repository 名字，名字格式是重点 必须是 yourname.github.io 比如 tom.github.io 我的是 asroads.github.io。

### 发布网站到GitHub远程

此时需要修改一下本地（/Users/hoolai/jsroads/myblog）的一个“_config.yml”记得一点 hexo 配置中 任何“:”后面都是带空格的。

```
deploy:
    type: git
    repository: https://github.com/asroads/asroads.github.io.git
    branch: master
```

如果你是第一次使用GitHub部署，还需要配置 SSH文件，另外如果已经配置了，或者多个账号用一台机器需要另行处理。以后这个详谈。首先我们需要检查一下本地有没有 SSH keys 。

```
ssh-keygen -t rsa -C "asroads@163.com"
```

在Git Bash输入以下指令（任意位置点击鼠标右键），检查是否已经存在了SSH keys。

```bash
ls -al ~/.ssh
```

如果已经存在或者正常生成了，用指令添加

```
eval `ssh-agent -s`
ssh-add
```

到了这里基本就搞定了，这个时候需要复制 key 到 github 上面 测试一下 输入以下指令

```
ssh -T git@github.com
```

这个时候 会看到输出警告，输入 “yes”

接下来需要我们配置一下 ‘~/.ssh/id_rsa.pub’ 里面的内容到GitHub上面

- 我们先去用户下找到这个文件，用文本编辑器打开，复制内容
- 打开GitHub网站，登录自己的账号 打开”settings“ — ”SSH and GPG keys” 然后添加一个“SSH keys”

此时 还需要安装 一个 hexo-deployer-git

```
npm install hexo-deployer-git --save
```

做完上面这些就表示已经搞定了。执行下面的命令，部署到github 上面吧

```
hexo generate
hexo deploy
```

最后打开你的网站博客查看效果吧。我的是 [asroads.github.io](http://www.asroads.com/2018/06/29/%E6%8B%93%E5%B1%95/%E6%80%8E%E4%B9%88%E7%94%A8HEXO-github%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E7%BD%91%E7%AB%99/asroads.github.io) 。

## 后记

在成功发布之后，禁不住内心一点小小的喜悦，学习过程中间经历了各种挫折、问题、报错、沮丧，经过辗转各大论坛终于得以解决，以后有空了，会慢慢的把自己所学所感写写出来。

十多年前（2007）入行的时候，回忆起学校老师在课堂上曾说，做技术的人，要有一个自己的门面（技术博客），这个是自己的一份“自我经营”，先后在新浪、网易博客、CSDN开了博客，由于后期的工作繁忙，奔波，懒散懈怠，大都虎头蛇尾，偶尔去打开想写点什么的时候，总感觉自己学的知识过于零碎，繁琐，整理起来过于麻烦，很多问题网上已经很多人都给出最好的答案，图文并茂的解答。再去造几个轮子，显得冗余了些。这篇文章也参考了网上许多优秀人的博客和解答，在此，衷心的谢谢他们，好人一生平安！

## 参考文档

[使用hexo搭建GitHub博客](http://yidao620c.github.io/2016/03/06/hexo.html)

[麦田守望者](https://seazhang.github.io/)

[Git 最著名报错 “ERROR: Permission to XXX.git denied to user”终极解决方案](https://www.jianshu.com/p/12badb7e6c10)

[Xcode中Command Line Tools安装方法](https://blog.csdn.net/chenyufeng1991/article/details/47007979)

[个性化图标生成网站](http://emblemmatic.org/markmaker/#/)

[HEXO主题配置网站](https://github.com/Sanonz/hexo-theme-concise/blob/master/README.md)