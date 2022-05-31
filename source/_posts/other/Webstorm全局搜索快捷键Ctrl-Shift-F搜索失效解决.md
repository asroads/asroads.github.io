---
title: Webstorm全局搜索快捷键Ctrl+Shift+F搜索失效解决
comments: true
categories: other
tags:
  - Windows
  - Webstorm
abbrlink: bb43a92e
date: 2022-04-22 14:25:23
---

最近家里重新安装系统，使用新装好webstorm打开项目后发现全局搜索快捷键（ctrl+shift+f）失效了，首先想到的快捷键冲突问题，一般由输入法快捷键冲突导致，由于是新电脑，所以使用的是微软自带的输入法。
<!--more-->

## 问题排查

打开设置下面的输入法 选项 果然找到了问题所在

![image-20220531142927480](Webstorm%E5%85%A8%E5%B1%80%E6%90%9C%E7%B4%A2%E5%BF%AB%E6%8D%B7%E9%94%AECtrl-Shift-F%E6%90%9C%E7%B4%A2%E5%A4%B1%E6%95%88%E8%A7%A3%E5%86%B3/image-20220531142927480.png)

关闭热键

![image-20220531143002184](Webstorm%E5%85%A8%E5%B1%80%E6%90%9C%E7%B4%A2%E5%BF%AB%E6%8D%B7%E9%94%AECtrl-Shift-F%E6%90%9C%E7%B4%A2%E5%A4%B1%E6%95%88%E8%A7%A3%E5%86%B3/image-20220531143002184.png)

搜索修好了

![image-20220531143036185](Webstorm%E5%85%A8%E5%B1%80%E6%90%9C%E7%B4%A2%E5%BF%AB%E6%8D%B7%E9%94%AECtrl-Shift-F%E6%90%9C%E7%B4%A2%E5%A4%B1%E6%95%88%E8%A7%A3%E5%86%B3/image-20220531143036185.png)

至此，可以webstorm中使用快捷键ctrl+shift+f就可以轻松调出全局搜索了。