---
title: Mac 上如何卸载Jenkins
date: 2018-08-13 17:40:20
categories: "tool"
tags:
- 技术
---

去年开始学习了一个持续集成工具Jenkins，这个工具很好用，可以对项目做持续集成，由于第一次安装之后还没有来得及多学习，就开始做项目功能，后面就没有细看，前段时间换了固态硬盘之后，重装了Mac系统，结果在安装Jenkins时候，保留了原来的设置，很麻烦，想卸载的彻底，于是在网上找到了这个办法，亲测有效。

<!-- more -->

- 第一种办法

```bash
/Library/Application Support/Jenkins/Uninstall.command
```

- 第二种办法

```
sudo launchctl unload /Library/LaunchDaemons/org.jenkins-ci.plist
sudo rm !$
sudo rm -rf /Applications/Jenkins "/Library/Application Support/Jenkins" /Library/Documentation/Jenkins
sudo rm -rf /Users/Shared/Jenkins
# if you want to get rid of all the jobs and builds:
sudo dscl . -delete /Users/jenkins
# delete the jenkins user and group (if you chose to use them):
sudo dscl . -delete /Groups/jenkins
```

