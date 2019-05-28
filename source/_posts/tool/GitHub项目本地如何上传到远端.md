---
title: GitHub项目本地如何上传到远端
date: 2019-04-21 19:36:27
categories: "tool"
tags:
- 技术
---

我们有时候自己在GitHub上面创建的仓库 有时候需要自己下载到本地编辑更新后上传，前面的命令都是正确的到到了最后的上传远端的步骤报错"The requested URL returned error: 403 Forbidden while accessing"，这个时候我们如何解决呢

<!-- more -->

### 解决步骤

- 第一步我们打开我们本地的目录下面的 .git 文件夹，如果没有，那就打开隐藏文件  隐藏文件命令是 `command+shift+.`
- 此时我们打开  .git/config 文件
- 修改  

```bash
[remote "origin"]
	url = https://github.com/asroads/example.git
```

为 

```bash
[remote "origin"]
	url = https://asroads@github.com/asroads/example.git
```



- 再次git push，弹出框输入密码，即可提交。