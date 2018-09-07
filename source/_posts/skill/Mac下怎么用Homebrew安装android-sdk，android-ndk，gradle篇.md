---
title: Mac下怎么用Homebrew安装android-sdk，android-ndk，gradle篇
date: 2018-06-23 10:13:44
tags:
---

### 安装 [Homebrew](https://brew.sh/index_zh-cn)

- 安装Homebrew：

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

**小提示:**如果想卸载Homebrew需要(*其实就是把“install”改成“uninstall”*)

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/uninstall)"
```

> 安装软件，如：brew install oclint 
>
> 卸载软件，如：brew uninstall oclint
>
> 搜索软件，如：brew search oclint 
>
> 更新软件，如：brew upgrade oclint 
>
> 查看安装列表， 如：brew list 
>
> 更新Homebrew，如：brew update

![001](Mac下怎么用Homebrew安装android-sdk，android-ndk，gradle篇/001.png)



### 安装 android-sdk

```bash
brew cask install android-sdk
```

**小提示**如果遇上Warning: File /Users/zjh/.android/repositories.cfg could not be loaded.

解决办法：

```
touch ~/.android/repositories.cfg
```

#### 配置Path环境

```
vim ~/.bash_profile
```

- 设置路径

  ~~~
  # Setting PATH for android-sdk
  export ANDROID_SDK_ROOT=/usr/local/share/android-sdk
  export PATH="${PATH}:${ANDROID_SDK_ROOT}/tools:${ANDROID_SDK_ROOT}/platform-tools"
  ~~~


### 安装 android-ndk

```
brew cask install android-ndk
```

#### 配置Path环境

~~~
vim ~/.bash_profile
~~~

- 设置路径

  ```
  # Setting PATH for android-ndk
  export ANDROID_NDK_ROOT=/usr/local/share/android-sdk
  export PATH="${PATH}:${ANDROID_NDK_ROOT}
  ```

### 安装Gradle

```
brew install gradle
```

#### 配置Path环境

```
vim .bash_profile
```

```
// 写入
export PATH="${PATH}:/usr/local/Cellar/gradle/4.0.1/bin"
```

**检查**

```
gradle -v
```

### 怎么解决 Environment variable $ANDROID_HOME not found

MacOS开发Android app经常会遇到环境的坑，$ANDROID_HOME 经常会遇到无法使用

* 首先找到sdk路径

安装有几个路径：

1. 从网站直接下载的 需要设置

```
ANDROID_HOME= .../ADT/sdk
```

2. 使用 Homebrew 下载的

```
ANDROID_HOME=/usr/local/Cellar/android-sdk/{YOUR_SDK_VERSION_NUMBER}
```

3. 跟随 Android Studio 一起安装

```
ANDROID_HOME=/Users/{YOUR_USER_NAME}/Library/Android/sdk
```

#### 配置

```
export ANDROID_HOME={YOUR_PATH}
#mine: export ANDROID_HOME=/Users/zwwill/Library/Android/sdk
#or  : export ANDROID_HOME=~/Library/Android/sdk
export PATH=$PATH:$ANDROID_HOME/tools:$ANDROID_HOME/platform-tools
```

或者将以上脚本配置到~/.bash_profile文件下

```
source ~/.bash_profile
```

---



**注意**

这样配置出来的环境 不一定适用于所有的机器，如果想全部安装最好还是安装 “Android Studio” 去安装SDK和NDK

---

