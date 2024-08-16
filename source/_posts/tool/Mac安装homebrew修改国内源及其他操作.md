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



## brew upgrade 升级软件包

```bash
# 检查更新
brew outdated
# 升级版本
brew upgrade
# 清理旧版本
brew cleanup
```

检查更新，列出所有已安装的软件包中有新版本的包

```bash
# 查看可更新的包
brew outdated
```

显示如下：

```bash
YDC012deMac-mini~(:|✔) % brew outdated
==> Auto-updating Homebrew...
Adjust how often this is run with HOMEBREW_AUTO_UPDATE_SECS or disable with
HOMEBREW_NO_AUTO_UPDATE. Hide these hints with HOMEBREW_NO_ENV_HINTS (see `man brew`).
==> Downloading https://ghcr.io/v2/homebrew/portable-ruby/portable-ruby/blobs/sha256:ce5c135bf98da783bf5964e89aab587e3341c8adc379f36ace17ae7881f53e8c
############################################################################################# 100.0%
==> Pouring portable-ruby-3.3.4_1.arm64_big_sur.bottle.tar.gz
glib (2.76.4, 2.78.4, 2.80.3) < 2.80.4
graphviz (11.0.0) < 12.0.0
harfbuzz (8.0.1, 8.3.0_1, 8.5.0) < 9.0.0
jenkins-lts (2.452.2) < 2.462.1
libavif (1.0.4) < 1.1.1
librsvg (2.58.1) < 2.58.2
libx11 (1.8.9) < 1.8.10
nvm (0.39.7) < 0.40.0
openjdk@21 (21.0.3) < 21.0.4
pcre2 (10.42, 10.43) < 10.44
pyenv (2.4.7) < 2.4.10
tree (2.1.1) < 2.1.3
xz (5.4.6) < 5.6.2
```

更新软件版本

```bash
# 更新所有软件包的版本
brew upgrade
# 更新指定软件包的版本
brew upgrade <package>
```

 显示如下：

```bash
YDC012deMac-mini~(:|✔) % brew upgrade
==> Upgrading 13 outdated packages:
pyenv 2.4.7 -> 2.4.10
tree 2.1.1 -> 2.1.3
libavif 1.0.4 -> 1.1.1
harfbuzz 8.5.0 -> 9.0.0
glib 2.80.3 -> 2.80.4
openjdk@21 21.0.3 -> 21.0.4
xz 5.4.6 -> 5.6.2
nvm 0.39.7 -> 0.40.0
librsvg 2.58.1 -> 2.58.2
graphviz 11.0.0 -> 12.0.0
pcre2 10.43 -> 10.44
jenkins-lts 2.452.2 -> 2.462.1
libx11 1.8.9 -> 1.8.10
==> Downloading https://ghcr.io/v2/homebrew/core/pcre2/manifests/10.44
############################################################################################# 100.0%
==> Fetching pcre2
==> Downloading https://ghcr.io/v2/homebrew/core/pcre2/blobs/sha256:78fe8885f70cc1ec83eefd678e1dfc3b
############################################################################################# 100.0%
==> Downloading https://ghcr.io/v2/homebrew/core/xz/manifests/5.6.2
############################################################################################# 100.0%
==> Fetching xz
==> Downloading https://ghcr.io/v2/homebrew/core/xz/blobs/sha256:5ec389ac6a0b190914be00c62d2de0a1826
############################################################################################# 100.0%
==> Downloading https://ghcr.io/v2/homebrew/core/glib/manifests/2.80.4
############################################################################################# 100.0%
==> Fetching dependencies for glib: python@3.12
==> Downloading https://ghcr.io/v2/homebrew/core/python/3.12/manifests/3.12.4
############################################################################################# 100.0%
==> Fetching python@3.12
==> Downloading https://ghcr.io/v2/homebrew/core/python/3.12/blobs/sha256:abcd749806f483941fe8b97fe3
############################################################################################# 100.0%
==> Fetching glib
==> Downloading https://ghcr.io/v2/homebrew/core/glib/blobs/sha256:e52c5b1cfc4a9c37fbd697a29d37c96d7
############################################################################################# 100.0%
==> Downloading https://ghcr.io/v2/homebrew/core/libx11/manifests/1.8.10
############################################################################################# 100.0%
==> Fetching libx11
==> Downloading https://ghcr.io/v2/homebrew/core/libx11/blobs/sha256:5467d41501260e483586e9b05ed3137
############################################################################################# 100.0%
==> Downloading https://ghcr.io/v2/homebrew/core/harfbuzz/manifests/9.0.0
############################################################################################# 100.0%
==> Fetching harfbuzz
==> Downloading https://ghcr.io/v2/homebrew/core/harfbuzz/blobs/sha256:9b683aa2fa183d1b26a5a2758663b
############################################################################################# 100.0%
==> Downloading https://ghcr.io/v2/homebrew/core/openjdk/21/manifests/21.0.4
############################################################################################# 100.0%
==> Fetching openjdk@21
==> Downloading https://ghcr.io/v2/homebrew/core/openjdk/21/blobs/sha256:9c746b162f12f2c3ac7ef4cca58
############################################################################################# 100.0%
==> Downloading https://ghcr.io/v2/homebrew/core/tree/manifests/2.1.3
############################################################################################# 100.0%
==> Fetching tree
==> Downloading https://ghcr.io/v2/homebrew/core/tree/blobs/sha256:f0ff7ec061de0b347ca0c735aa199f30c
############################################################################################# 100.0%
==> Downloading https://ghcr.io/v2/homebrew/core/libavif/manifests/1.1.1
############################################################################################# 100.0%
==> Fetching libavif
==> Downloading https://ghcr.io/v2/homebrew/core/libavif/blobs/sha256:d933afea7d7bce60d38fc145804997
############################################################################################# 100.0%
==> Downloading https://ghcr.io/v2/homebrew/core/nvm/manifests/0.40.0
############################################################################################# 100.0%
==> Fetching nvm
==> Downloading https://ghcr.io/v2/homebrew/core/nvm/blobs/sha256:af3f9692df19ac0f8599ea485d68a42b0a
############################################################################################# 100.0%
==> Downloading https://ghcr.io/v2/homebrew/core/librsvg/manifests/2.58.2
############################################################################################# 100.0%
==> Fetching librsvg
==> Downloading https://ghcr.io/v2/homebrew/core/librsvg/blobs/sha256:62e368378f7593288d519f0e2d3244
############################################################################################# 100.0%
==> Downloading https://ghcr.io/v2/homebrew/core/graphviz/manifests/12.0.0
############################################################################################# 100.0%
==> Fetching graphviz
==> Downloading https://ghcr.io/v2/homebrew/core/graphviz/blobs/sha256:c790fb58a57e9a2f14aa39a9f4800
############################################################################################# 100.0%
==> Downloading https://ghcr.io/v2/homebrew/core/jenkins-lts/manifests/2.462.1
############################################################################################# 100.0%
==> Fetching jenkins-lts
==> Downloading https://ghcr.io/v2/homebrew/core/jenkins-lts/blobs/sha256:20440dea5cdb741d4b256aa55b
############################################################################################# 100.0%
==> Downloading https://ghcr.io/v2/homebrew/core/pyenv/manifests/2.4.10
############################################################################################# 100.0%
==> Fetching pyenv
==> Downloading https://ghcr.io/v2/homebrew/core/pyenv/blobs/sha256:c6d9edeec3354839bf5f6843cf7a2437
############################################################################################# 100.0%
==> Upgrading pcre2
  10.43 -> 10.44
==> Pouring pcre2--10.44.arm64_sonoma.bottle.tar.gz
🍺  /opt/homebrew/Cellar/pcre2/10.44: 237 files, 6.3MB
==> Running `brew cleanup pcre2`...
Disable this behaviour by setting HOMEBREW_NO_INSTALL_CLEANUP.
Hide these hints with HOMEBREW_NO_ENV_HINTS (see `man brew`).
Removing: /opt/homebrew/Cellar/pcre2/10.42... (230 files, 6.2MB)
Removing: /opt/homebrew/Cellar/pcre2/10.43... (234 files, 6.3MB)
==> Upgrading xz
  5.4.6 -> 5.6.2
==> Pouring xz--5.6.2.arm64_sonoma.bottle.tar.gz
🍺  /opt/homebrew/Cellar/xz/5.6.2: 96 files, 1.9MB
==> Running `brew cleanup xz`...
Removing: /opt/homebrew/Cellar/xz/5.4.6... (164 files, 2.6MB)
==> Upgrading glib
  2.80.3 -> 2.80.4
==> Installing dependencies for glib: python@3.12
==> Installing glib dependency: python@3.12
==> Downloading https://ghcr.io/v2/homebrew/core/python/3.12/manifests/3.12.4
Already downloaded: /Users/ydc012/Library/Caches/Homebrew/downloads/2970a992f26f411cf71250ce54eb24411d8b065824b51819da7085559f09ce66--python@3.12-3.12.4.bottle_manifest.json
==> Pouring python@3.12--3.12.4.arm64_sonoma.bottle.tar.gz
==> /opt/homebrew/Cellar/python@3.12/3.12.4/bin/python3.12 -Im ensurepip
==> /opt/homebrew/Cellar/python@3.12/3.12.4/bin/python3.12 -Im pip install -v --no-index --upgrade -
🍺  /opt/homebrew/Cellar/python@3.12/3.12.4: 3,279 files, 65.8MB
==> Installing glib
==> Pouring glib--2.80.4.arm64_sonoma.bottle.tar.gz
🍺  /opt/homebrew/Cellar/glib/2.80.4: 526 files, 36.2MB
==> Running `brew cleanup glib`...
Removing: /opt/homebrew/Cellar/glib/2.76.4... (455 files, 22.2MB)
Removing: /opt/homebrew/Cellar/glib/2.78.4... (456 files, 22.5MB)
Removing: /opt/homebrew/Cellar/glib/2.80.3... (526 files, 36.2MB)
==> Upgrading libx11
  1.8.9 -> 1.8.10
==> Pouring libx11--1.8.10.arm64_sonoma.bottle.tar.gz
🍺  /opt/homebrew/Cellar/libx11/1.8.10: 1,043 files, 7MB
==> Running `brew cleanup libx11`...
Removing: /opt/homebrew/Cellar/libx11/1.8.9... (1,043 files, 7.0MB)
==> Upgrading harfbuzz
  8.5.0 -> 9.0.0
==> Pouring harfbuzz--9.0.0.arm64_sonoma.bottle.tar.gz
🍺  /opt/homebrew/Cellar/harfbuzz/9.0.0: 77 files, 9.7MB
==> Running `brew cleanup harfbuzz`...
Removing: /opt/homebrew/Cellar/harfbuzz/8.0.1... (76 files, 9MB)
Removing: /opt/homebrew/Cellar/harfbuzz/8.3.0_1... (76 files, 9.5MB)
Removing: /opt/homebrew/Cellar/harfbuzz/8.5.0... (77 files, 9.7MB)
==> Upgrading openjdk@21
  21.0.3 -> 21.0.4
==> Pouring openjdk@21--21.0.4.arm64_sonoma.bottle.tar.gz
🍺  /opt/homebrew/Cellar/openjdk@21/21.0.4: 602 files, 331.4MB
==> Running `brew cleanup openjdk@21`...
Removing: /opt/homebrew/Cellar/openjdk@21/21.0.3... (601 files, 331.3MB)
==> Upgrading tree
  2.1.1 -> 2.1.3
==> Pouring tree--2.1.3.arm64_sonoma.bottle.tar.gz
🍺  /opt/homebrew/Cellar/tree/2.1.3: 9 files, 182KB
==> Running `brew cleanup tree`...
Removing: /opt/homebrew/Cellar/tree/2.1.1... (8 files, 177.2KB)
==> Upgrading libavif
  1.0.4 -> 1.1.1
==> Pouring libavif--1.1.1.arm64_sonoma.bottle.tar.gz
🍺  /opt/homebrew/Cellar/libavif/1.1.1: 21 files, 641.4KB
==> Running `brew cleanup libavif`...
Removing: /opt/homebrew/Cellar/libavif/1.0.4... (20 files, 556.5KB)
==> Upgrading nvm
  0.39.7 -> 0.40.0
==> Pouring nvm--0.40.0.all.bottle.tar.gz
==> Caveats
Please note that upstream has asked us to make explicit managing
nvm via Homebrew is unsupported by them and you should check any
problems against the standard nvm install method prior to reporting.

You should create NVM's working directory if it doesn't exist:
  mkdir ~/.nvm

Add the following to your shell profile e.g. ~/.profile or ~/.zshrc:
  export NVM_DIR="$HOME/.nvm"
  [ -s "/opt/homebrew/opt/nvm/nvm.sh" ] && \. "/opt/homebrew/opt/nvm/nvm.sh"  # This loads nvm
  [ -s "/opt/homebrew/opt/nvm/etc/bash_completion.d/nvm" ] && \. "/opt/homebrew/opt/nvm/etc/bash_completion.d/nvm"  # This loads nvm bash_completion

You can set $NVM_DIR to any location, but leaving it unchanged from
/opt/homebrew/Cellar/nvm/0.40.0 will destroy any nvm-installed Node installations
upon upgrade/reinstall.

Type `nvm help` for further information.
==> Summary
🍺  /opt/homebrew/Cellar/nvm/0.40.0: 10 files, 202.0KB
==> Running `brew cleanup nvm`...
Removing: /opt/homebrew/Cellar/nvm/0.39.7... (9 files, 194.2KB)
==> Upgrading librsvg
  2.58.1 -> 2.58.2
==> Pouring librsvg--2.58.2.arm64_sonoma.bottle.tar.gz
==> /opt/homebrew/opt/gdk-pixbuf/bin/gdk-pixbuf-query-loaders --update-cache
🍺  /opt/homebrew/Cellar/librsvg/2.58.2: 23 files, 45.9MB
==> Running `brew cleanup librsvg`...
Removing: /opt/homebrew/Cellar/librsvg/2.58.1... (23 files, 46.1MB)
==> Upgrading graphviz
  11.0.0 -> 12.0.0
==> Pouring graphviz--12.0.0.arm64_sonoma.bottle.tar.gz
🍺  /opt/homebrew/Cellar/graphviz/12.0.0: 283 files, 7.2MB
==> Running `brew cleanup graphviz`...
Removing: /opt/homebrew/Cellar/graphviz/11.0.0... (283 files, 7.2MB)
==> Upgrading jenkins-lts
  2.452.2 -> 2.462.1
==> Pouring jenkins-lts--2.462.1.all.bottle.tar.gz
==> Caveats
Note: When using launchctl the port will be 8080.

To start jenkins-lts now and restart at login:
  brew services start jenkins-lts
Or, if you don't want/need a background service you can just run:
  /opt/homebrew/opt/openjdk@21/bin/java -Dmail.smtp.starttls.enable\=true -jar /opt/homebrew/opt/jenkins-lts/libexec/jenkins.war --httpListenAddress\=127.0.0.1 --httpPort\=8080
==> Summary
🍺  /opt/homebrew/Cellar/jenkins-lts/2.462.1: 9 files, 92.5MB
==> Running `brew cleanup jenkins-lts`...
Removing: /opt/homebrew/Cellar/jenkins-lts/2.452.2... (9 files, 92.6MB)
==> Upgrading pyenv
  2.4.7 -> 2.4.10
==> Pouring pyenv--2.4.10.arm64_sonoma.bottle.tar.gz
🍺  /opt/homebrew/Cellar/pyenv/2.4.10: 1,217 files, 3.5MB
==> Running `brew cleanup pyenv`...
Removing: /opt/homebrew/Cellar/pyenv/2.4.7... (1,215 files, 3.5MB)
==> Caveats
==> nvm
Please note that upstream has asked us to make explicit managing
nvm via Homebrew is unsupported by them and you should check any
problems against the standard nvm install method prior to reporting.

You should create NVM's working directory if it doesn't exist:
  mkdir ~/.nvm

Add the following to your shell profile e.g. ~/.profile or ~/.zshrc:
  export NVM_DIR="$HOME/.nvm"
  [ -s "/opt/homebrew/opt/nvm/nvm.sh" ] && \. "/opt/homebrew/opt/nvm/nvm.sh"  # This loads nvm
  [ -s "/opt/homebrew/opt/nvm/etc/bash_completion.d/nvm" ] && \. "/opt/homebrew/opt/nvm/etc/bash_completion.d/nvm"  # This loads nvm bash_completion

You can set $NVM_DIR to any location, but leaving it unchanged from
/opt/homebrew/Cellar/nvm/0.40.0 will destroy any nvm-installed Node installations
upon upgrade/reinstall.

Type `nvm help` for further information.
==> jenkins-lts
Note: When using launchctl the port will be 8080.

To start jenkins-lts now and restart at login:
  brew services start jenkins-lts
Or, if you don't want/need a background service you can just run:
  /opt/homebrew/opt/openjdk@21/bin/java -Dmail.smtp.starttls.enable\=true -jar /opt/homebrew/opt/jenkins-lts/libexec/jenkins.war --httpListenAddress\=127.0.0.1 --httpPort\=8080
YDC012deMac-mini~(:|✔) %
```

安装新版本后，默认会执行以下命令将旧版本的安装包删除，如下图所示

```bash
brew cleanup <package>
```

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

