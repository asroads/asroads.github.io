---
title: Mac下怎么配置 Java JDK
categories: tool
tags:
  - Mac
  - 技术
abbrlink: d2199953
date: 2018-06-18 10:48:11
---

- 首先打开.bash_profile 文件

在用户目录 可以使用 “shift + command+.”快捷键 显示和隐藏 文件

<!-- more -->

```bash
# Setting PATH for JAVA jdk1.8.0_181
JAVA_HOME="/Library/Java/JavaVirtualMachines/jdk1.8.0_181/Contents/Home/"
CLASS_PATH="$JAVA_HOME/lib"
PATH=".:$PATH:$JAVA_HOME/bin"
```

- 然后测试

```bash
echo $JAVA_HOME
```

电脑输出 路径

```bash
/Library/Java/JavaVirtualMachines/jdk1.8.0_181/Contents/Home/
```

- 还可以 输入 

```bash
java -version
```

查看输出

```bash
java version "1.8.0_181"
Java(TM) SE Runtime Environment (build 1.8.0_181-b13)
Java HotSpot(TM) 64-Bit Server VM (build 25.181-b13, mixed mode)
```

- 然后查看 "Javac -version"是否和“Java -version”版本一致

```bash
jsroadsdeiMac:~ jsroads$ javac -version
javac 1.8.0_181
```

最后献上我自己本地最后的“.bash_profile” 文件

```bash

# Setting PATH for Python 2.7
# The original version is saved in .bash_profile.pysave
PATH="/Library/Frameworks/Python.framework/Versions/2.7/bin:${PATH}"
export PATH

# Setting PATH for Python 3.6
# The original version is saved in .bash_profile.pysave
PATH="/Library/Frameworks/Python.framework/Versions/3.6/bin:${PATH}"
export PATH

# Setting PATH for JAVA jdk1.8.0_181
JAVA_HOME="/Library/Java/JavaVirtualMachines/jdk1.8.0_181/Contents/Home"
CLASS_PATH="$JAVA_HOME/lib"
PATH=".:$PATH:$JAVA_HOME/bin"

export PATH=$PATH:/usr/local/bin/gradle


export ANDROID_HOME=/Users/jsroads/Library/Android/sdk
export PATH=${PATH}:${ANDROID_HOME}/tools
exportPATH=${PATH}:${ANDROID_HOME}/platform-tools

export PATH=$PATH:/Users/jsroads/Library/Android/android-ndk-r13b
ANDROID_NDK_HOME=/Users/jsroads/Library/Android/android-ndk-r13b
export ANDROID_NDK_HOME 

```

如果是Windows 环境 

> (1)新建->变量名"JAVA_HOME"，变量值"C:\Java\jdk1.8.0_05"（即JDK的安装路径） 
> (2)编辑->变量名"Path"，在原变量值的最后面加上“;%JAVA_HOME%\bin;%JAVA_HOME%\jre\bin” 
> (3)新建->变量名“CLASSPATH”,变量值“.;%JAVA_HOME%\lib;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar”

