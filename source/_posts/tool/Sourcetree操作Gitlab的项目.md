---
title: Sourcetree操作Gitlab的项目
comments: true
categories: tool
tags:
  - Sourcetree
  - Gitlab
abbrlink: d9ac0497
date: 2020-02-10 20:05:51
---

疫情当前，免不了远程办公，公司的项目用的是SVN版本控制，这次在家远程办公后，准备把项目迁移到GitLab上面，本身这个没有什么可以记录的，但我还是在操作过程中遇到了一个错误，花费了几个小时时间搜索查找错误，最终得以解决。
<!--more-->

### 错误fatal: could not read Username for 'http://xxxxxx.com'

#### 现象

使用 命令行 可以正常 更新项目，使用Sourcetree 更新报错

```shell
fatal: could not read Username for 'http://xxxxxx.com': Device not configured
```

找了好些答案，有的说要git 改成系统的  我更改后 确实可以更新了，但是不显示 推送啥的 每次特别麻烦

后面继续查找，找到了一个操作，终于得到解决：

#### 解决办法

原问题：

> 我在mac OS上不像这篇文章：[`git push origin `throws error`HTTP Basic：Access denied`](https://stackoverflow.com/questions/43085424/git-push-origin-branchname-throws-error-http-basic-access-denied) 我在GitLab中配置了密码。我在GitLab上创建项目后创建了SSL密钥。
>
> 当我将现有文件夹用于新项目时，请按照以下步骤操作：
>
> **现有文件夹**
>
> - cd existing_folder
> - git init
> - git remote add origin https://gitlab.com/sobopla/Geronimod.git
> - git add。
> - git commit -m“初始提交”
> - git push -u origin master
>
> 我被提示输入我的GitLab用户名和密码。输入密码后，我得到：`remote: HTTP Basic: Access denied fatal: Authentication failed for 'https://gitlab.com/myname/myproject'`

回答者：

> 对于Mac用户：
>
> 1. 转到钥匙串并删除gitlab帐户
> 2. 转到终端中的项目路径，然后输入git pull
> 3. 然后，系统会要求您输入gitlab的用户名和密码
> 4. 输入您的用户名，您可以在配置文件部分的gitlab帐户中找到该用户名。
> 5. 然后在此输入您更新的密码。
> 6. 在这里，我们再次尝试推送您的代码，它可能会帮助你们。

总结：

我就是按照这个办法去操作，然后问题得以解决， 猜想，我Gitlab 更改过用户名和密码 是不是以前在电脑里有存储，所以报错呢？

按照他的这个回答，成功解决这个报错。

## 修改文件名大小写"造成的git上传文件丢失

1、查看是否忽略大小写

```
git config core.ignorecase
```

  true为忽略大小写

  false为不忽略大小写

2、运行`git config core.ignorecase false`，关闭git忽略大小写配置

### 参考链接：

- [GitLab remote：HTTP Basic：拒绝访问和致命身份验证](https://xbuba.com/questions/47860772)
- [mac下gitLab、sourceTree的配合使用](https://www.jianshu.com/p/707de2a1046d)
- [修改文件名大小写"造成的git上传文件丢失](https://my.oschina.net/huibaifa/blog/3096818) 

