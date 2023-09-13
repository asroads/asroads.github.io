---
title: 如何在Mac上配置一个本地Web服务器
comments: true
categories: other
tags:
  - Mac
  - 技术
abbrlink: fa75ff33
date: 2023-09-13 20:28:51
---

以前一直本地Web服务器使用Tomcat搭建，虽然搭建过程并不复杂，但是对于很多场景来说，还是略嫌麻烦，有没有更快，更简单的方式呢？答案是有的，下面就简单的介绍两种方式。
<!--more-->

## 为什么需要服务器

通常情况下，我们希望可以通过在浏览器中直接打开或者通过一个 URL 来访问一个文件。在有些情况下，打开一个本地的 html 文件时，会出现运行错误。其中包含了异步请求。一些浏览器（包括 Chrome）将不会运行其中的异步请求（请参阅 从服务器获取数据）。 这是因为安全限制而导致的，此外执行文件中的代码时需要通过执行一些附加逻辑（如 PHP 或 Python）才能获得结果。

### 方法一、启动 Mac 自带的 Apache 服务器

1. 运行 Apache `$ sudo apachectl start`
2. 退出 Apache `$ sudo apachectl stop`
3. 把工程文件夹放到以下位置中 `/Library/WebServer/Documents`
4. 在浏览器中访问：在地址栏中输入地址 `http://localhost/工程文件夹名称/`，回车。

**注意：** 不再需要使用后一定要记得退出，否则会消耗电脑性能。

Q：如何修改 Apache 的默认端口？
A：首先，找到 Apache 的配置文件，位于 `/etc/apache2` 下的 `httpd.conf`。
然后，找到 `Listen 80` 那一行，修改成你想要的端口即可。

### 方法二：借助 Mac 系统自带的 Python，使用 SimpleHTTPServer 模块启动服务器

1. 安装 Python，其实 Mac 系统就自带了 Python2.7。可以通过输入 python 查看当前的python版本

2. 通过 `cd <your-working-dir>` 进入到你的工作目录下，也就是你要让别人访问的文件所在的目录。

3. 在工作目录下执行下面的命令后，就可以启动服务了：

   ```shell
   # 如果你的 Python 版本是 3.X
   python3 -m http.server
   
   # 如果你的 Python 版本是 2.X
   python -m SimpleHTTPServer
   ```

4. 默认情况下，上面的操作将会在本地 Web 服务器上的端口 8000 上运行工作目录中的内容。您可以通过在浏览器中输入 URL `http://localhost:8000` 并回车，来访问此服务器。你会看到列出的目录的内容，点击就可以查看你想运行的 HTML 文件。

Q：如何修改服务器的默认端口？
A：可以通过运行下面的命令来指定一个端口号：

```shell
# 如果你的 Python 版本是 3.X
python -m http.server <your-port> 

# 如果你的 Python 版本是 2.X
python -m SimpleHTTPServer <your-port>
```

Q：如何关闭 `http-server`服务

A：按快捷键`CTRL-C`

### 结论

方法二相比方法一来说，更简单、方便，也更灵活，此外还可以随时在终端上看到服务器的状态。

## 参考

- [Mac自带Apache服务器的使用](https://www.jianshu.com/p/90d5fa728861)
- [如何设置一个本地测试服务器？【MDN】](https://developer.mozilla.org/zh-CN/docs/Learn/Common_questions/Tools_and_setup/set_up_a_local_testing_server)