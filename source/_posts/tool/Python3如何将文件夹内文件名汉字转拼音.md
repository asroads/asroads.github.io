---
title: Python3如何将文件夹内文件名汉字转拼音
comments: true
categories: tool
tags:
  - Python
abbrlink: 4e1d3fc2
date: 2021-05-10 13:03:18
---

游戏开发中项目内大家沟通基本都是使用汉字，但是程序内基本使用英文，很多游戏道具或者物品，这个时候有时候为了便于查找都是用拼音配置图标名字或者人物图片等，这个时候我们需要对文件重新命名，把汉字一个个修改成拼音，此时问题来了，这样道具要存两份吗，显然是不必要的，那么一个需求就产生了，如何批量修改文件的名字的呢，下面就说说使用`Python`如何操作。
<!--more-->

## 环境

- 确保 `Python3`环境
- pip 安装 xpinyin 

首先确保 `pip install xpinyin`

下面是我们ReName.py 的代码：

```python
# -*-coding=utf8-*-
from xpinyin import Pinyin
import os
import sys
def renameCurrentDirectory():
    resume_rootdir = '.'
    renameDirectoryByPath(resume_rootdir)
def renameDirectoryByPath(dirPath):
    print(u'dirPath：'+dirPath)
    print(u'重命名开始！')
    pin = Pinyin()
    llist = os.listdir(dirPath)
    for i in range(0, len(llist)):
        print(u'现在进行第{}个'.format(i))
        resume = os.path.join(dirPath, llist[i])
        if os.path.isfile(resume):
            obj = os.path.basename(resume)
            if obj[0] == '.':
                continue
            print(u'开始处理  {}'.format(obj))
            pinyin_name = pin.get_pinyin(obj, "")
            print(u'{} 新名字是:{}'.format(obj, pinyin_name))
            Newdir = os.path.join(dirPath, pinyin_name)  # 新的文件路径
            os.rename(resume, Newdir)  # 重命名
    print(u'重命名结束！')

if __name__ == '__main__':
    args = sys.argv
    if len(args) == 1:
        print("没有传入路径，当前目录执行")
        renameCurrentDirectory()
        # sys.exit()
    else:
        print("传入路径:",args[1])
        execel_path = args[1]
        renameDirectoryByPath(execel_path)


```

## 用法

不传入路径参数  直接修改当前目录

```python
python3 ReName.py # ReName.py 为脚本名称
```

或者 传入路径参数  修改传入路径的目录

```python
python3 ReName.py /Users/smile/work/文档/我的小镇/trunk/美术/ui/物品图标 

# ReName.py 为脚本名称
# /Users/smile/work/文档/我的小镇/trunk/美术/ui/物品图标 为修改的目录路径
```

### 执行结果：

![image-20210510131720859](Python3如何将文件夹内文件名汉字转拼音/image-20210510131720859.png)

## 参考

- [Python将文件夹内文件名汉字转拼音](https://blog.csdn.net/weixin_44870139/article/details/109541704)