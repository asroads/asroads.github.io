---
title: 如何把别人的PR在GitHub页面做成补丁应用到自己的仓库
comments: true
categories: tool
tags:
  - Mac
  - GitHub
abbrlink: 87189f27
date: 2024-12-22 23:57:11
---

我们时常在技术论坛或者阅读别人的的技术帖子，看到有人粘贴出GitHub某个PR的地址，以前每次自己都是一个个文件下载，并且把更新同步到自己本地仓库，于是想，有没有办法做成补丁文件呢？是的，我们可以在 GitHub 页面上把别人的 PR 变成 补丁（patch）文件，然后在本地仓库应用这个补丁，即使你的本地仓库和远程仓库不是同一个 Git

<!--more-->

## 直接下载 PR 作为补丁并应用（适用于不同 Git 仓库）

GitHub 允许你直接获取 PR 的 **patch 文件**，然后你可以在本地应用它。

#### **步骤：**

1. **下载 PR 补丁文件**

- 你可以在浏览器地址栏直接访问：

```shell
https://github.com/REPO_OWNER/REPO_NAME/pull/PR_NUMBER.patch
```

例如，如果 PR 链接是：

```shell
https://github.com/someone/cool-project/pull/42
```

你可以访问

```shell
https://github.com/someone/cool-project/pull/42.patch
```

这样可以下载一个 `.patch` 文件。

2. **在本地仓库应用补丁**

   进入你的本地 Git 仓库：    

   ```shell
   cd /path/to/your/local-repo
   ```

   然后应用补丁：

   ```shell
   git apply /path/to/downloaded.patch
   ```

   或者如果你想让 Git 识别这次更改并保留提交历史解决可能的冲突后，提交代码：

   ```shell
   git am < /path/to/downloaded.patch
   ```

   解决可能的冲突后，提交代码：

   ```shell
   git commit -m "Apply PR #42 patch from someone/cool-project"
   ```

   最后推送到远程仓库：

   ```shell
   git push origin main
   ```

如果你希望合并 PR 但本地不是同一个 Git 仓库，下载并应用 .patch 文件 是最优雅的方法 🚀。