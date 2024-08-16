---
title: 如何同时安装多个版本的Python3
comments: true
categories: tool
tags:
  - Python
abbrlink: 286c50d7
date: 2024-08-09 14:25:17
---

安装多个版本的Python3是一种常见需求，特别是在开发过程中，由于不同项目可能依赖于不同的Python版本。使用**pyenv**可以让你在同一台机器上安装并管理多个Python版本。`pyenv`通过改变环境变量来切换全局Python版本，同时它也支持项目级别的Python版本管理。
<!--more-->

## 如何安装

`pyenv` 是一个用于管理多个 Python 版本的工具，它使得在同一台机器上轻松切换和管理不同的 Python 版本成为可能。下面是使用 `pyenv` 的一些基本步骤：

### 1. 安装 `pyenv`

在大多数 Linux 和 macOS 系统上，你可以通过以下命令安装 `pyenv`：

```bash
curl https://pyenv.run | bash
```

之后，你需要将 `pyenv` 添加到 shell 环境中。在 `~/.bashrc`, `~/.bash_profile`, `~/.zshrc` 等文件中添加以下内容：

```bash
export PATH="$HOME/.pyenv/bin:$PATH"
eval "$(pyenv init --path)"
eval "$(pyenv init -)"
eval "$(pyenv virtualenv-init -)"
```

然后重新加载 shell 配置文件：

```bash
source ~/.bashrc  # 如果你使用的是 Bash
# 或
source ~/.zshrc   # 如果你使用的是 Zsh
```

### 2. 安装 Python 版本

使用 `pyenv install` 命令来安装你需要的 Python 版本。例如：

```bash
pyenv install 3.9.6
pyenv install 2.7.18
```

安装`pyenv`之后，可以通过`pyenv install 3.x.x`来安装不同版本的Python。其中“3.x.x”是你需要安装的Python版本号。安装完成后，可以使用`pyenv global 3.x.x`命令来设置系统级别的Python版本，或者使用`pyenv local 3.x.x`为当前目录设置特定的Python版本，这使得在不同项目之间切换Python版本变得非常容易。

## 如何使用

### 1. 列出已安装的 Python 版本

使用 `pyenv versions` 命令可以查看当前已安装的 Python 版本：

```bash
pyenv versions
```

### 2. 设置全局 Python 版本

你可以使用 `pyenv global` 命令设置一个全局的 Python 版本，这个版本将用于系统的默认 Python 解释器：

```bash
pyenv global 3.9.6
```

### 3. 为某个项目设置本地 Python 版本

你还可以在某个项目目录下为该项目设置专用的 Python 版本：

```bash
cd your_project_directory
pyenv local 3.8.10
```

这样，在该目录下的所有 Python 操作都会使用指定的版本。

### 4. 使用不同的 Python 版本

如果你需要临时切换到另一个 Python 版本，可以使用 `pyenv shell` 命令：

```bash
pyenv shell 2.7.18
```

这个设置只会在当前的 shell 会话中生效。

### 5. 卸载 Python 版本

如果你不再需要某个 Python 版本，可以使用 `pyenv uninstall` 命令来卸载它：

```bash
pyenv uninstall 3.9.6
```

### 6. 使用 `pyenv` 管理虚拟环境

`pyenv` 还可以与 `pyenv-virtualenv` 插件一起使用来管理虚拟环境。你可以使用 `pyenv virtualenv` 命令来创建和管理虚拟环境。

例如，创建一个基于 Python 3.8.10 的虚拟环境：

```bash
pyenv virtualenv 3.8.10 my-virtual-env
```

激活该虚拟环境：

```bash
pyenv activate my-virtual-env
```

### 7. 常见问题和调试

如果在安装 Python 版本时遇到问题，通常是因为缺少依赖项。你可以参考 `pyenv` 提示的信息安装必要的依赖项。

通过这些基本步骤，你就可以开始使用 `pyenv` 来管理你的 Python 环境了。

在 `pyenv` 中，你可以使用以下命令来查看可以安装的 Python 版本列表：

```bash
pyenv install --list
```

这将列出所有可以通过 `pyenv` 安装的 Python 版本，包括官方发布的版本、开发版本、Anaconda 版本等。你可以从列表中选择你想要安装的版本。

如果你只想查看某个特定版本的可用版本，例如 `3.8`，你可以使用以下命令：

```bash
pyenv install --list | grep 3.8
```

这将只显示与 `3.8` 相关的版本。

## 拓展

### demjson 报错 use_2to3 is invalid

在配置 Python3 环境时，安装 demjson 2.2.4 出现以下报错：

```shell
error in demjson setup command: use_2to3 is invalid
```

网上搜着查看了一下报错的位置，发现是由于 demjson 2.2.4 兼容 Python2 和 Python3 ，当安装环境为 Python3 时，有一部分代码需要做相应的转换, 而 Setuptools 从版本 58.0.0 开始不再支持 2to3 的 builds ，所以导致 demjson 2.2.4 安装后不再可用，需要降级 setuptools 版本即可解决

```shell
pip install --upgrade setuptools==57.5.0
```

### 强制安装（不推荐）

你可以使用 `--break-system-packages` 选项强制安装包，但这可能会导致系统的 Python 环境出现问题，不推荐使用：

```
pip3 install yagmail --break-system-packages
```

#### 其他方案

js2py替换demjson

```python
import js2py

def convert_js_to_json(js_content):
    """
    使用 js2py 将 JavaScript 对象转换为 Python 字典（相当于 JSON）
    :param js_content: JavaScript 对象的字符串
    :return: Python 字典
    """
    # 将 JavaScript 对象代码封装在一个表达式中
    js_code = f"var obj = {js_content}; obj;"
    
    # 使用 js2py 执行 JavaScript 并获取结果
    js_result = js2py.eval_js(js_code)
    
    # 将 JavaScript 对象转换为 Python 字典
    json_obj = js_result.to_dict()
    
    return json_obj

# 使用示例
js_content = """
{
    platform: "ios",
    groupList: [
        "default"
    ],
    collisionMatrix: [
        [
            true
        ]
    ],
    hasResourcesBundle: false,
    hasStartSceneBundle: false,
    remoteBundles: [],
    subpackages: [],
    launchScene: "db://assets/Scene/helloworld.fire",
    orientation: "",
    server: "",
    debug: true,
    jsList: [],
    bundleVers: {
        internal: "d36c4",
        main: "b6176"
    }
}
"""

# 使用示例
json_obj = convert_js_to_json(js_content)
print("json_obj", json_obj)

```

## 参考

- [如何同时安装多个版本的Python3](https://docs.pingcode.com/ask/ask-ask/174272.html)