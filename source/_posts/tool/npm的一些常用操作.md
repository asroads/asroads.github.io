---
title: npm的一些常用操作
comments: true
categories: tool
tags:
  - npm
abbrlink: aac44fb0
date: 2023-12-09 22:16:31
---

客户端开发Javascript很多时候都会用到npm的类库或者操作命令，很多时候，本地要么要切换仓库镜像地址，要么要清理缓存，要么是版本不对，很多操作，每次用到时候都要去搜索，很是麻烦，于是下面就记录一些常用的操作，后面方便自己查阅和使用。
<!--more-->
先说结论：**node的默认镜像是国外的，修改镜像为国内地址**

### npm安装慢 ，卡在sill idealTree buildDeps不动

以前电脑上安装一个类库挺快的，不知道从哪天起，突然就安装一直卡着安装不动了，仔细看上面的错误都是network错误，于是查阅，发现其他人也遇到这样的问题，请缓存也不管用，于是换了镜像地址，只要配置 registry 即可：

```shell
npm config set registry https://registry.npmmirror.com
```

查看当前的镜像网址 ：

```shell
npm config get registry 
```

使用taobao镜像地址（建议使用上面的）

```shell
npm config set registry https://registry.npm.taobao.org 
```

### 使用nvm切换 Node.js 版本

比如：要将默认的 Node.js 版本设置为 v16.20.2，并确保 `node` 和 `stable` 这两个别名都指向这个版本，你需要使用 `nvm alias` 命令来更新这些别名。以下是具体的步骤：

1. **设置 `node` 别名**：这将使 `node` 指向版本 v16.20.2。

   ```shell
   nvm alias node 16.20.2
   ```

2. **设置 `stable` 别名**：这将使 `stable` 也指向版本 v16.20.2。

   ```shell
   nvm alias stable 16.20.2
   ```

3. **设置默认版本**：这将使得每次打开新的终端会话时，都会自动使用 v16.20.2。

   ```shell
   nvm alias default 16.20.2
   ```

执行这些命令后，`node` 和 `stable` 应该都会指向版本 v16.20.2。你可以通过再次运行 `nvm list` 来确认这一点。这样，无论你运行 `nvm use node`、`nvm use stable` 还是新开一个终端窗口，都会使用 v16.20.2 版本的 Node.js。

### 使用 nvm 复制依赖包`reinstall-packages` 命令

当使用 `nvm`（Node Version Manager）切换到不同的 Node.js 版本时，之前安装的 npm 包不会自动传递到新版本。这是因为 `nvm` 为每个 Node.js 版本维护独立的环境和包存储位置。但是，有方法可以帮助快速地重新安装这些包，使用命令可以用来将一个版本的已安装包复制到另一个版本。例如，如果你从 Node.js 版本 10 切换到版本 12 并希望安装所有在版本 10 中安装的包，你可以运行：

```shell
nvm reinstall-packages 10
```

展示全局安装的依赖包：

```shell
npm list -g --depth=0
```

### npm缓存如何清理

在命令行中使用 "npm cache clean --force" 可以清理 npm 缓存。这将强制删除所有缓存数据。

如果只想清理全局缓存，可以使用 "npm cache clean --force -g"。如果只想清理本地缓存，可以使用 "npm cache clean --force --no-global"。

```shell
npm cache clean --force
```

```shell
npm cache clean --force -g
```

```shell
npm cache clean --force --no-global
```

### 添加权限

```shell
sudo chown -R 501:20 /Users/ydc012/.nvm
```

```shell
sudo chown -R $(whoami) ~/.npm
```

