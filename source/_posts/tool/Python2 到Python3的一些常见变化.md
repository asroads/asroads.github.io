---
title: Python2 到Python3的一些常见变化
date: 2019-06-25 19:20:12
categories: "tool"
tags:
- Python
---

有时候工作过程中需要用Python处理一些项目资源或者表格的转换，目前比较常用的都是Python3，但是因为很多以前的开发者用的都是Python2的语法，所以要想让Python2的写的文件，能在Python3环境正常运行，需要知道其中的一些变化，这样稍作处理和更改，便可以让原来的脚本可以在新的环境内正常运行。(持续更新)

<!-- more -->

### python中的reload方法

在python2中，reload()是内置函数，能够直接使用。但是在python3中，直接使用reload()会报错，需要从imp中导入。用法为

```python
from imp import reload
reload(xyz)
```

### python中的reload方法 xrange 定义

解决办法：

python3 中取消了 range 函数，而把 xrange 函数重命名为 range，所以现在直接用 range 函数即可。

### try... except Exception,e: 不能用

解决办法：

```python
try:
    ...
except Exception as e:
    ...
```

### 报“TypeError: a bytes-like object is required, not ‘str’ ”

意思是“**类型错误：需要类似字节的对象，而不是字符串**”。报错原因：python3和Python2在套接字返回值解码上有区别。

解决办法不难，只需要用上python的bytes和str两种类型转换的函数encode()、decode()即可！

- str通过encode()方法可以编码为指定的bytes；
- 反过来，如果我们从网络或磁盘上读取了字节流，那么读到的数据就是bytes。要把bytes变为str，就需要用decode()方法；
- 因此：我只需要把上图中的代码改成下面的即可！

```python
import os,sys

#打开文件
fd = os.open('foo.txt',os.O_RDWR|os.O_CREAT)

str = 'this is fujieace.com test'
str = str.encode()

#写入字符串
os.write(fd,str)

#关闭文件
os.close(fd)

print('关闭文件成功！')
```

### python中引入包的时候报错AttributeError: module 'sys' has no attribute 'setdefaultencoding'

比如：下面这样写 会报错 AttributeError: module 'sys' has no attribute 'setdefaultencoding'

```python
import sys
from imp import reload
imp.reload(sys)
sys.setdefaultencoding('utf-8')
```

原因：Python3字符串默认编码unicode, 所以sys.setdefaultencoding也不存在了

Python3解决办法：去掉，sys.setdefaultencoding

```python
#sys.setdefaultencoding('utf-8')
```



