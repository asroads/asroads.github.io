---
title: Tree展示工具tree-node-cli介绍
comments: true
categories: tool
tags:
  - tree
  - npm
abbrlink: 289f6da2
date: 2022-10-21 15:34:59
---

tree-node-cli 是一个 Node.js 编写的命令行工具，用于在终端中以树形结构展示目录和文件。相比系统自带的 tree 命令，tree-node-cli 具有更加友好的输出更多功能。
<!--more-->

## 安装

在 Windows 10 系统中，需要先安装 Node.js 和 npm。然后在命令行中输入以下命令进行安装：

```
npm install tree-node-cli
# or
npm install -g tree-node-cli
```

安装完成后，可以使用 tree/treee命令在命令行中调用该工具。

## 命令用法

使用 --help 命令列出帮助信息。

```
treee -h
```

指定路径的级别（扫描的深度）。
使用 -l 级别编号 指定路径级别。

```
treee -l 2
```



### 其他参数

选项：

```
  -V, --version             输出版本号。
  -a, --all-files           打印所有文件，包括隐藏文件。
  --dirs-first              先列出目录，再列出文件。
  -d, --dirs-only           仅列出目录。
  -s, --sizes               打印每个文件的大小（以字节为单位）和名称。
  -I, --exclude [patterns]  排除与模式匹配的文件。 | 可以分隔多个模式。将整个模式用双引号括起来。例如：`"node_modules|coverage"`。
  -L, --max-depth <n>       目录树的最大显示深度。
  -r, --reverse             将输出按照字母表的反序排序。
  -F, --trailing-slash      对于目录追加“/”。
  -S, --line-ascii          打开 ASCII 行图形。
  -h, --help                显示帮助信息。
```

