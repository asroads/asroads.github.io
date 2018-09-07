---
title: 怎么把Hexo网站备份到GitHub上
date: 2018-08-10 20:56:51
categories: "other"
tags:
- hexo
- 技术
---

### 备份背景

很多人用了Hexo+GitHub搭建了自己的博客，但是重装系统或者换了电脑之后，怎么迁移成了新的一个问题，因为发布在GitHub上面的是静态网页，已经编译好的没有办法重新恢复还原，我找了很多方法，发现有一个方法很好用。

### 备份环境

我们假设博客已经成功发布切写过文章

1. 在你的博客仓库创建一个分支back（这个命名随意）
2. ~~设置back为默认分支（不知道怎么设的可以百度）~~(这个目前已经被GitHub官方禁止，可以跳过)
3. 将博客仓库clone至本地，将之前的back文件夹中的
   `_config.yml`，`themes/`，`source`，`scffolds/`，`package.json`，`.gitignore`复制到你克隆下来的仓库文件夹，即Username.github.io；（Username是你自己的用户名）
4. 将themes/concise/(我用的是concise主题)中的`.git/`和`.gitignore`删除，否则无法将主题文件夹push ，注意要把concise下面的`.gitignore`文件一起删除，否则不能成功。
5. 在Username.github.io；文件夹执行`npm install`，`npm install hexo-deployer-git`(这里可以看看分支是不是显示为Hexo)
6. 执行`git add`，`git commit -m "提交文件"`，`git push origin back`来提交back网站源文件
7. 执行hexo g -d 生成静态网页部署到github上，这样，Username.github.io仓库就有master分支保存静态网页，hexo分支保存源文件

### 如何更改

- 执行git add，git commit -m "提交文件"，git push origin Hexo来提交Hexo网站源文件
- 执行hexo g、 hexo d 生成静态网页部署到github上

### 如何恢复

- 安装git、安装Nodejs和npm、使用克隆命令将仓库拷贝至本地
- 在文件夹内执行命令`npm install hexo-cli -g`、`npm install`、`npm install hexo-deployer-git`
- 如果 “hexo s”命令没有 则需要安装 sever模块  执行hexo server前，先下载server模块，npm install hexo-server

```bash
npm install hexo-server
```

### 注意事项

Hexo的源文件说明：

- _config.yml站点的配置文件，需要拷贝；
- themes/主题文件夹，需要拷贝；
- source博客文章的.md文件，需要拷贝；
- scaffolds/文章的模板，需要拷贝；
- package.json安装包的名称，需要拷贝；
- .gitignore限定在push时哪些文件可以忽略，需要拷贝；
- .git/主题和站点都有，标志这是一个git项目，不需要拷贝；
- node_modules/是安装包的目录，在执行npm install的时候会重新生成，不需要拷贝；
- public是hexo g生成的静态网页，不需要拷贝；
- .deploy_git同上，hexo g也会生成，不需要拷贝；
- db.json文件，不需要拷贝。

**不需要拷贝的文件正是.gitignore中所忽略的**

### 可能用到的命令

```
npm install hexo-cli -g
```

```
npm install hexo --save
```

```
npm install hexo-deployer-git --save
```

如果没有 `hexo s` 命令 运行

```
npm install hexo-server --save
```

然后发现  `hexo s` 就可以正常用了！

### 参考文章

- [怎么去备份你的Hexo博客](https://www.jianshu.com/p/baab04284923)