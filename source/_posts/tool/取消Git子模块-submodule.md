---
title: 取消Git子模块(submodule)
comments: true
categories: tool
tags:
  - git
abbrlink: '535121e9'
date: 2021-02-22 17:56:15
---

有时候我们因项目需求，需要添加或者取消git 的子模块 此时需要如何操作呢？下面简单的说一下操作技巧。
<!--more-->
如果只需要将子模块代码放入主存储库，则只需删除子模块，然后将文件重新添加到主存储库中即可：

```
git rm --cached submodule_path # delete reference to submodule HEAD (no trailing slash)
git rm .gitmodules             # if you have more than one submodules,
                               # you need to edit this file instead of deleting!
rm -rf submodule_path/.git     # make sure you have backup!!
git add submodule_path         # will add files instead of commit reference
git commit -m "remove submodule"
```

