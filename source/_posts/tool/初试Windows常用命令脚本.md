---
title: 初试Windows常用命令脚本
comments: true
categories: tool
tags:
  - Shell
abbrlink: aa0cec47
date: 2022-07-15 16:04:33
---

之前开发一直使用的是`nodejs`或者`Python`作为脚本工具，近来使用`Windows`环境，于是就开始写了两个`Shell`脚本，有了一点小心得，于是记录一下，自己的学习路程，便于后面自己查阅。
<!--more-->
常见的一些命令：

打印：

```shell
echo "Hello" #就是打印出来 hello 字符串
```

变量：

```shell
${a} #其中a是变量 格式就是用 $和{} 包裹
```

sleep  睡眠

```shell
sleep 1s    睡眠1秒

sleep 1m   睡眠1分

sleep 1h   睡眠1小时
```

要修改文件的内容，可以用如下的命令

```shell
sed -i 's/被替换的内容/要替换成的内容/g' file
```

### 常用的一些用法

#### 脚本调用另一个脚本的三种方法

假如有脚本 `first.sh`：

```shell
#!/bin/bash
#------------------
#@file:first.sh
#------------------
echo 'I am first file'
```

如何在其它`Shell`脚本中调用`first.sh`呢？主要有三种方法：source、点号以及sh命令。

1. source

```shell
#!/bin/bash
#---------------
#@file:second.sh
#---------------
echo 'Hello World'
source first.sh

```

1. 点号

```shell
#!/bin/bash
echo 'Hello World'
. first.sh
```

3. sh 命令

```shell
#!/bin/bash
echo 'Hello World'
sh  first.sh
```

三者输出的结果都是：

```shell
Hello World
I am first file
```

三者的区别:使用 source 命令和点号是等价的，类似于 C/C++ 中的 #include 预处理指令，都是将指定的脚本内容拷贝至当前的脚本中，由一个 Shell 进程来执行。使用sh命令来调用另外的脚本和前面两种方法有着本质的区别。使用sh命令则会开启新的Shell进程来执行指定的脚本，这样的话，父进程中的变量在子进程中就无法访问


### 常见的一些错误：

#### command not found"报错处理

###### 定义`STR1`变量，值为hello

```shell
# 定义STR1变量，值为abc

STR1 = "hello"(错误写法)

STR1="hello"(正确写法)
```

###### 比较两个字符串是否相等中if语句的写法

```shell
#!/bin/bash

STR1="1234"
STR2="12345"

#if[$STR1=$STR2](错误写法)
if [ $STR1 = $STR2 ] ;then
　　echo "相等"
else
　　echo "不相等"
fi
```

##### 运行shell脚本报错：Permission denied

问题原因：`=`右边多了一个空格

### 实践检验

最后是学习后写的两个小游戏的两个脚本：

`CopyScript.sh` 小游戏构建后 替换 代码脚本

```shell

#!/bin/sh
#------------------
#小游戏仅仅构建代码替换工具脚本
#------------------

#--------------- 项目构建所指向路径
source_folder="D:\Build\mini\wxbuild"

#--------------- Release 完整路径 ---------------
rc_source_path=$source_folder"\wechatgame\src\project.js"
#--------------- debug 完整路径 ---------------
debug_source_path=$source_folder"\wechatgame\src\project.dev.js"

#--------------- 微信开发者工具项目运行所指向路径 ---------------
target_path="D:\Build\mini\wx\wechatgame\engine\src\project.js"


echo "代码复制开始运行..."
#--------------- Debug ---------------
if test -e $debug_source_path
then
	echo "------------本次构建为 Debug 调试模式------------"
	echo $debug_source_path "exist!"
	cp $debug_source_path $target_path
	echo "Debug模式代码复制完毕！"
else
	echo "Debug file doesn't exist"
fi


#--------------- Release ---------------
if test -e $rc_source_path
then
	echo "------------本次构建为 Release 调试模式------------"
	echo $rc_source_path "exist!"
	cp $rc_source_path $target_path
	echo "Release 模式 代码复制完毕！"
else
	echo "Release file doesn't exist"
fi

echo "代码复制完成！！"
exec /bin/bash
```

`CustomMoveFolder.sh`

```shell
#!/bin/sh
#------------------
#小游戏完整构建代码替换工具脚本
#------------------

# 首屏相关 把远程资源挪出去

#------------------ 微信开发者运行项目父目录
project_wx="wx"
#------------------ Cocos Creator构建路径 
build_wx="wxbuild"


#------------------ 工具打开CDN资源对应本地目录
project_wx_cdn_res=$project_wx"/res"

#------------------ 工具打开项目根目录
project_wx_game=$project_wx"/wechatgame"
#------------------ Cocos Creator构建路径
wxbuild_path=$build_wx"/wechatgame"

if [ -d $project_wx_cdn_res ]
then
	echo "准备删除原"$project_wx_cdn_res"..."
	rm -rf $project_wx_cdn_res
	echo $project_wx_cdn_res"删除完毕！"
fi

if [ -d $project_wx_game ]
then
	rm -rf $project_wx_game
	echo $project_wx_game"存在删除..."
fi

if [ -d $wxbuild_path ]
then
	echo "开始复制"$wxbuild_path" 到 "$project_wx_game"..."
	cp -a $wxbuild_path $project_wx
	echo "复制操作结束准备下一步工作..."

	sleep 1s
	
	#source ./wx/wechatgame/autorun.sh
	if [ -d $project_wx"/res" ]
	then
		rm -rf $project_wx"/res"
		echo $project_wx"文件夹存在删除..."
	fi
	
	mv $project_wx_game"/res" $project_wx"/res"
	
	mv $project_wx_game"/res_internal" $project_wx_game"/res"
	
	if [ -d $project_wx_game"/engine" ]
	then
		echo $project_wx_game"文件夹已经存在，无需新建"
	else
		mkdir $project_wx_game"/engine"
		echo $project_wx_game"/engine文件夹不存在，新建文件夹..."
	fi
	
	if test -e  $project_wx_game"/adapter-min.js"
	then
		mv $project_wx_game"/adapter-min.js" $project_wx_game"/engine"
	fi
	
	if test -e  $project_wx_game"/adapter.js"
	then
		mv $project_wx_game"/adapter.js" $project_wx_game"/engine"
	fi
	mv $project_wx_game"/ccRequire.js" $project_wx_game"/engine"
	mv $project_wx_game"/game.js" $project_wx_game"/engine"
	mv $project_wx_game"/main.js" $project_wx_game"/engine"
	mv $project_wx_game"/src" $project_wx_game"/engine"
	mv $project_wx_game"/GameBackup.js" $project_wx_game"/game.js"
	sed -i 's/cocos\/cocos2d-js-min.js/..\/cocos\/cocos2d-js-min.js/g' $project_wx_game\/engine\/game.js
	
	echo "首屏操作OK！"
	echo "小游戏全部操作结束，打开工具调试吧！"
else
    echo $wxbuild_path"不存在"
	#mkdir $build_wx
fi

exec /bin/bash

```

源码 [点击前往](https://github.com/jsroads/mylibs/tree/main/minigame/buildScript)

## 参考

- [bash shell sleep_Shell脚本中让进程休眠的方法](https://blog.csdn.net/weixin_28771631/article/details/113028516)
- [Shell 脚本调用另一个脚本的三种方法](https://blog.csdn.net/k346k346/article/details/86751705)
- [关于在shell中运行命令，提示permission denied](https://segmentfault.com/q/1010000015015729)