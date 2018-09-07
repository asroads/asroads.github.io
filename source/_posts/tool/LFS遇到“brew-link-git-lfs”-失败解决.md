---
title: LFS遇到“brew link git-lfs” 失败解决
date: 2018-07-12 17:39:43
categories: "tool"
tags:
- git-lfs
---

### 现象描述

```
Warning: git-lfs 2.5.1 is already installed, it's just not linked
You can use `brew link git-lfs` to link this version.
localhost:~ jsroads$ brew link git-lfs
Linking /usr/local/Cellar/git-lfs/2.5.1...
Error: Could not symlink share/man/man5/git-lfs-config.5
/usr/local/share/man/man5 is not writable.
localhost:~ jsroads$
```

### 解决办法

This is probably an error related to permissions.

Try

```
sudo chown -R $(whoami) /usr/local/share/
```

or, if you only need this on the git-core path

```
sudo chown -R $(whoami) /usr/local/share/git-core 
```

might be enough.

Then run

```
brew link git
```

again.



最后 问题解决

```
localhost:~ jsroads$ sudo chown -R $(whoami) /usr/local/share/
Password:
Sorry, try again.
Password:
localhost:~ jsroads$ brew link git-lfs
Linking /usr/local/Cellar/git-lfs/2.5.1... 32 symlinks created
localhost:~ jsroads$

```

