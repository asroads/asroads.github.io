---
title: Hello World
categories: other
tags:
  - 技术
  - hexo
abbrlink: 4a17b156
date: 2018-05-22 12:52:48
---

[TOC]

Welcome to [Hexo](https://hexo.io/)! This is your very first post. Check [documentation](https://hexo.io/docs/) for more info. If you get any problems when using Hexo, you can find the answer in [troubleshooting](https://hexo.io/docs/troubleshooting.html) or you can ask me on [GitHub](https://github.com/hexojs/hexo/issues).

<!-- more -->

## Quick Start 快速引导

### Create a new post 新建页面

``` bash
$ hexo new "My New Post"
```

More info: [Writing](https://hexo.io/docs/writing.html)

### Run server 本地预览

``` bash
$ hexo server
```

More info: [Server](https://hexo.io/docs/server.html)

### Generate static files 构建站点

``` bash
$ hexo generate
```

More info: [Generating](https://hexo.io/docs/generating.html)

### Deploy to remote sites 部署站点到远程

``` bash
$ hexo deploy
```

More info: [Deployment](https://hexo.io/docs/deployment.html)

## 配置信息简介

| 参数        | 描述                                                         |
| :---------- | :----------------------------------------------------------- |
| title       | 网站标题                                                     |
| subtitle    | 网站副标题                                                   |
| description | 网站描述，主要用于 SEO，告诉搜索引擎关于站点的简要信息       |
| keywords    | 网站的关键词。使用半角逗号, 分隔多个关键词。                 |
| author      | 你的名字                                                     |
| language    | 网站使用的语言，常见的有 zh-Hans 、zh-CN 、 en               |
| timezone    | 网站时区。默认使用本地时区。也可以指定其它时区，如 America/New_York, Japan, 和 UTC 。一般的，对于中国大陆地区可以使用 Asia/Shanghai。 |

### 网站信息

| 参数                       | 描述                                                         | 默认值                         |
| :------------------------- | :----------------------------------------------------------- | :----------------------------- |
| url                        | 网址                                                         |                                |
| root                       | 网站根目录                                                   |                                |
| permalink                  | 文章的永久链接                                               | 格式 :year/:month/:day/:title/ |
| permalink_defaults         | 永久链接中各部分的默认值                                     |                                |
| pretty_urls                | 改写 permalink 的值来美化 URL                                |                                |
| pretty_urls.trailing_index | 是否在永久链接中保留尾部的 index.html，设置为 false 时去除   | true                           |
| pretty_urls.trailing_html  | 是否在永久链接中保留尾部的 .html, 设置为 false 时去除 (对尾部的 index.html无效) | true                           |

### 目录信息

| 参数         | 描述                                                         | 默认值         |
| :----------- | :----------------------------------------------------------- | :------------- |
| source_dir   | 资源文件夹，这个文件夹用来存放博客 `md` 等文件。             | source         |
| public_dir   | 公共文件夹，这个文件夹用于存放生成的站点静态文件。           | public         |
| tag_dir      | 标签文件夹                                                   | tags           |
| archive_dir  | 归档文件夹                                                   | archives       |
| category_dir | 分类文件夹                                                   | categories     |
| code_dir     | `Include code` 文件夹，`source_dir` 下的子目录               | downloads/code |
| i18n_dir     | 国际化（i18n）文件夹                                         | :lang          |
| skip_render  | 跳过指定文件的渲染。匹配到的文件将会被不做改动地复制到 `public` 目录中。您可使用 `glob` 表达式来匹配路径。 |                |

### 分页信息

| 参数             | 描述                                | 默认值 |
| :--------------- | :---------------------------------- | :----- |
| `per_page`       | 每页显示的文章量 (0 = 关闭分页功能) | `10`   |
| `pagination_dir` | 分页目录                            | `page` |

### 扩展信息

| 参数             | 描述                                                         |
| :--------------- | :----------------------------------------------------------- |
| `theme`          | 当前主题名称。值为`false`时禁用主题                          |
| `theme_config`   | 主题的配置文件。在这里放置的配置会覆盖主题目录下的 `_config.yml` 中的配置 |
| `deploy`         | 部署部分的设置                                               |
| `meta_generator` | [Meta generator](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/meta#属性) 标签。 值为 `false` 时 Hexo 不会在头部插入该标签 |

## 包括或不包括目录和文件

在 `Hexo` 配置文件中，通过设置 `include/exclude` 可以让 `Hexo` 进行处理或忽略某些目录和文件夹。可以使用 [glob 表达式](https://github.com/isaacs/minimatch) 对目录和文件进行匹配。

`include` 和 `exclude` 选项都只能应用于 `source/` 文件夹, 但 `ignore` 选项可以应用于所有文件夹。

| 参数      | 描述                                                         |
| :-------- | :----------------------------------------------------------- |
| `include` | `Hexo` 默认会忽略隐藏文件和文件夹（包括名称以下划线和 `.` 开头的文件和文件夹，`Hexo` 的 `_posts` 和 `_data` 等目录除外）。通过设置此字段将使 Hexo 处理他们并将它们复制到 `source` 目录下。 |
| `exclude` | `Hexo` 会忽略这些文件和目录                                  |
| `ignore`  | 忽略文件或文件夹                                             |

## 参考

- [使用Hexo+github pages+travis ci搭建好看的个人博客](https://mfrank2016.github.io/breeze-blog/2020/05/03/hexo/hexo-config/)

