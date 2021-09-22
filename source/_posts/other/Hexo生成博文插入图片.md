---
title: Hexo生成博文插入图片
categories: other
tags:
  - hexo
  - 技术
abbrlink: 95d84581
date: 2018-06-06 18:20:04
---

### 发现问题

学习的路上，真是艰辛，前面的语法标识学习都还好，这次遇到图片插入，已经转换成本地路径了，结果依旧是部署之后，显示不出来，看到这个，感觉有点方了！

<!-- more -->

俗话说，世上无难事只怕有心人，遇到问题不可怕，可怕的是逃避问题，于是乎我搜索开始，然后终于解决，方法参考网上某位仁兄的方法，特别有效！

### 思考问题

##### 第一步

- 把主页配置文件`_config.yml` 里的`post_asset_folder:`这个选项设置为`true`
- 在你的hexo目录下执行这样一句话`npm install hexo-asset-image --save`，这是下载安装一个可以上传本地图片的插件，来自dalao：[dalao的git](https://github.com/CodeFalling/hexo-asset-image)
- 等待一小段时间后，再运行`hexo n "xxxx"`来生成md博文时，`/source/_posts`文件夹内除了`xxxx.md`文件还有一个**同名的文件夹**

##### 第二步

- 最后在`xxxx.md`中想引入图片时，先把图片复制到xxxx这个文件夹中，然后只需要在xxxx.md中按照markdown的格式引入图片：

  `![你想输入的替代文字](xxxx/图片名.jpg)`

- **注意：** xxxx是这个md文件的名字，也是同名文件夹的名字。只需要有文件夹名字即可，不需要有什么绝对路径。你想引入的图片就只需要放入xxxx这个文件夹内就好了，很像引用相对路径。

- 最后检查一下，`hexo g`生成页面后，进入`public\2017\02\26\index.html`文件中查看相关字段，可以发现，html标签内的语句是`<img src="2017/02/26/xxxx/图片名.jpg">`，而不是`<img src="xxxx/图片名.jpg>`。这很重要，关乎你的网页是否可以真正加载你想插入的图片。

### 2021-09-22 更新

如果遇到图无法显示的情况，此时需要检查一下 网页的源代码：

此时 `config.yml` 文件

```json
url: https://jsroads.gitee.io
permalink: :year/:month/:day/:title/
```

但是我们打印出来的图片路径 是：

```bash
update link as:-->/.io//image-20210922130821755.png
update link as:-->/.io//image-20210922130821755.png
```

经过查找问题出在 `项目根目录/node_modules/hexo-asset-image/index.js`的23行左右

```javascript
    if(/.*\/index\.html$/.test(link)) {
      // when permalink is end with index.html, for example 2019/02/20/xxtitle/index.html
      // image in xxtitle/ will go to xxtitle/index/
      appendLink = 'index/';
      var endPos = link.lastIndexOf('/');
    }
    else {
      var endPos = link.lastIndexOf('.');
    }
```

发现 这个走了 else 分支导致的，我们把 `link.lastIndexOf('.')` 修改为 `link.lastIndexOf('/')` 即可 修改后

```javascript
    if(/.*\/index\.html$/.test(link)) {
      // when permalink is end with index.html, for example 2019/02/20/xxtitle/index.html
      // image in xxtitle/ will go to xxtitle/index/
      appendLink = 'index/';
      var endPos = link.lastIndexOf('/');
    }
    else {
      var endPos = link.lastIndexOf('/');
    }
```

重新 清理运行 ： `hexo clean & hexo g`

```
update link as:-->/2021/09/18/hello-world/image-20210922130821755.png
update link as:-->/2021/09/18/hello-world/image-20210922130821755.png
```

然后运行效果 正常

### 总结

这个是 相对路径和绝对路径的问题，HEXO 在编译 部署 发布过程中使用的是默认的相对路径，而我们插入图片的时候使用的是本地的绝对路径，这样就导致了图片显示不出来的问题。

#### 参考

Noob的博客： [hexo生成博文插入图片](https://blog.csdn.net/sugar_rainbow/article/details/57415705)