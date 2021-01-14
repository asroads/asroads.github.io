---
title: MAC电脑批量删除GitHub仓库(repositories)
comments: true
categories: other
tags:
  - Mac
  - GitHub
abbrlink: 6e618a28
date: 2021-01-01 14:21:54
---

有时候GitHub社区看到好的仓库就fork一下，时间久了有些仓库过时了，有些是作者不维护了，有些和自己现在的工作内容相差太远，想删除一些无用过期的仓库，但是看了一下，没有批量操作的捷径，一个个操作感觉太麻烦，于是网上找了一下还真有对应的方法。
<!--more-->

## 背景

自己的仓库 太多，一个个删除麻烦

![image-20210114143711289](MAC电脑批量删除GitHub仓库-repositories/image-20210114143711289.png)

下面就把步骤记录一下：

## 步骤

### 创建token

登录Github，按导航 “Settings/Developer settings”，切换到Tab “Personal access tokens”， 新生成一个token。选择 scope， “delelete_repo”，然后复制 token 内容。后面有用。一次性操作，建议scope尽可能设置小范围，并且操作后删除该token。

### 配置删除repo列表

按导航“settings/repositories”，选取并复制所有repo，粘贴到一个“repos.txt”文本文件，处理后得到一个自己准备删除的repo列表。

比如我的：

![image-20210114143032795](MAC电脑批量删除GitHub仓库-repositories/image-20210114143032795.png)

```
jsroads/d3
jsroads/FNT-Creator
jsroads/cocos2d-js-1
jsroads/cocos2d-js
jsroads/cocos-docs
jsroads/ProtoBuf.js
jsroads/flow
jsroads/Cocos2d-x-js-3-docs
jsroads/jikexueyuanSecret
jsroads/cocos2d-java
jsroads/JavaScriptLessons20140305
jsroads/physics-example
jsroads/egf-protobuf
jsroads/cocos_creator_resource
```

### API文档中的删除接口（了解即可）

### 构建请求脚本（Example：python）

```python
from time import sleep
import requests

headers = {
    "Accept": "application/vnd.github.v3+json",
    "Authorization": "token XXXXXXXXXXXXXXXXXXXXXX", # 此处的XXX代表上面的token
    "X-OAuth-Scopes": "repo"
}

with open('./repos.txt', 'r', encoding='utf-8') as f:
    data = f.readlines()

url = "https://api.github.com/repos/{}/{}"
urls = []
for line in data:
    name, repo = line.strip().split("/")
    urls.append(url.format(name, repo))

for l in urls:
    requests.delete(url=l, headers=headers)
    sleep(2)

```

然后填写 自己的 复制的token 替换 脚本内的 `XXXXXXXXXXXXXXXXXXXXXX`

### 运行脚本 等待结束

![image-20210114143557784](MAC电脑批量删除GitHub仓库-repositories/image-20210114143557784.png)

重新刷新网页：

![image-20210114143750487](MAC电脑批量删除GitHub仓库-repositories/image-20210114143750487.png)

仓库已经被删除。

### 删除我们创建的token

此时我们的工作完成了，上面申请的token 没用了，为了避免后面减少不必要的麻烦，还是去删除掉。

![image-20210114144057060](MAC电脑批量删除GitHub仓库-repositories/image-20210114144057060.png)

## 参考

- [批量删除GitHub仓库-简书](https://www.jianshu.com/p/308b85e1fe69)
- [GitHub批量删除repository](https://blog.csdn.net/ouening/article/details/106109370)

