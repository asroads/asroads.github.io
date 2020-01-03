---
title: Python：PNGJPG Convert to Webp
categories: tool
tags:
  - 技术
  - Python
abbrlink: '1515441'
date: 2018-08-18 20:06:31
---

### 学习背景

之前做项目的时候，看到公司的高手把Png图片压缩成webp 格式的文件，一直感觉很高大上，没有机会瞻仰真容，后来在自己学习的时候，遇到几篇不错的博客，拜读之后，发现似乎没有想象的那么难，于是乎，就想自己学习一下这个转换格式。

<!-- more -->

### 运行环境

- webp环境
- 可以用houmebrew 安装（有Mac工作的同学可以看看什么是Homebrew）

### 编译环境

1. webp环境 

```bash
$brew install webp
```

安装实用程序完成后，就可以使用 cwebp命令 将 JPEG 或 PNG 图像转换成 webp 格式， 也可以通过dwebp 命令将 webp 图像转换回 PNG、PAM、PPM 或 PGM 图像。

示例

- 其他格式图片转 webp

```
$cwebp [options] -q quality input.jpg -o output.webp  
```

质量选项应该是 0 (差)到 100 (很好)之间的数字，典型的质量值大约是 80，也可以多尝试几个数值，自己比较一下效果，根据自己的需要选择。本人推荐转图片时质量用的是80

- webp 转其他格式

```bash
$dwebp input_file.webp [options] [-o output_file]
```

2. python 环境

(待日后补充)