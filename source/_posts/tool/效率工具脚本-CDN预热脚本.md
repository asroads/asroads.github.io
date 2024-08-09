---
title: 效率工具脚本-CDN预热脚本
comments: true
categories: tool
tags:
  - nodejs
  - Python
abbrlink: d81ac378
date: 2024-07-29 13:04:02
---

最近游戏随着体量不断增多，有时候版本更新内容很多，此时使用了CDN预热功能，这个界面是可以手动预热地址和上传文件的，于是我就写了一个CDN预热脚本，下面就简单的记录一下。
<!--more-->

## 介绍

现在市场上 流行的脚本，基本都是Python和nodejs，下面这个脚本分别给出。

### python脚本版本

![image-20240729130922845](./效率工具脚本-CDN预热脚本/image-20240729130922845.png)

cdn_main.py 源码如下：

```python
import os
import shutil


def get_valid_directory(prompt):
    while True:
        path = input(prompt).strip().replace(" ", "")
        if not os.path.exists(path):
            print(f"路径不存在: {path}")
        elif not os.path.isdir(path):
            print(f"这不是一个有效的目录: {path}")
        else:
            return path


def compare_directories(prev_dir, curr_dir):
    new_files = []
    existing_files = []

    for root, _, files in os.walk(curr_dir):
        for file in files:
            curr_file_path = os.path.join(root, file)
            rel_path = os.path.relpath(curr_file_path, curr_dir)
            prev_file_path = os.path.join(prev_dir, rel_path)

            if not os.path.exists(prev_file_path):
                new_files.append(curr_file_path)
            else:
                existing_files.append(curr_file_path)

    return new_files, existing_files


def classify_files_by_extension(files, extensions):
    classified_files = {ext: [] for ext in extensions}
    other_files = []

    for file in files:
        _, ext = os.path.splitext(file)
        ext = ext.lower()
        if ext in classified_files:
            classified_files[ext].append(file)
        else:
            other_files.append(file)

    return classified_files, other_files


def sort_files_by_size(files, descending=True):
    return sorted(files, key=lambda file: os.path.getsize(file), reverse=descending)


def add_url_prefix(files, prefix, base_path):
    return [os.path.join(prefix, os.path.relpath(file, base_path)) for file in files]


def save_classified_files(classified_files):
    output_dir = "output"

    # 清空 output 目录
    if os.path.exists(output_dir):
        shutil.rmtree(output_dir)
        print(f"已清空目录: {output_dir}")
    os.makedirs(output_dir)

    for ext, files in classified_files.items():
        file_path = os.path.join(output_dir, f"{ext[1:]}.txt")
        with open(file_path, 'w') as f:
            for file in files:
                f.write(f"{file}\n")


def main():
    previous_version_path = get_valid_directory("请输入上一个版本的remote目录路径: ")
    current_version_path = get_valid_directory("请输入当前要热更版本的remote目录路径: ")

    new_files, existing_files = compare_directories(previous_version_path, current_version_path)

    extensions_input = input("请输入要分类的后缀名（多个后缀用逗号分隔，直接回车统计所有后缀）: ").strip()
    if extensions_input:
        extensions = extensions_input.split(',')
        extensions = [ext.strip().lower() if ext.startswith('.') else '.' + ext.strip().lower() for ext in extensions]
    else:
        extensions = set(os.path.splitext(file)[1].lower() for file in new_files)

    classified_files, other_files = classify_files_by_extension(new_files, extensions)

    sort_order = input("请输入排序规则 (默认从大到小排序，输入N从小到大排序): ").strip().upper()
    descending = sort_order != 'N'

    for ext, files in classified_files.items():
        classified_files[ext] = sort_files_by_size(files, descending=descending)

    url_prefix = input("请输入要添加的URL前缀: ").strip()
    for ext, files in classified_files.items():
        classified_files[ext] = add_url_prefix(files, url_prefix, current_version_path)

    save_classified_files(classified_files)


if __name__ == "__main__":
    main()

```

#### 使用说明

1. 使用脚本运行 `python3 cdn_main.py`
2. 依次根据提示输入对应目录以及规则
3. 等待完成

```shell
Last login: Sat Jul 27 14:32:08 on ttys000
YDC012deMac-mini~/TestCase/Python/hot_cdn_plus(main|●6✚2…) % python3 cdn_main.py
请输入上一个版本的remote目录路径: /Users/ydc012/Downloads/old
请输入当前要热更版本的remote目录路径: /Users/ydc012/Downloads/new
请输入要分类的后缀名（多个后缀用逗号分隔，直接回车统计所有后缀）:
请输入排序规则 (默认从大到小排序，输入N从小到大排序):
请输入要添加的URL前缀: https://xxxxx/tcs/mini/wx/publish/wechatgame_release/remote
已清空目录: output
YDC012deMac-mini~/TestCase/Python/hot_cdn_plus(main|●6✚2…) %
```

#### 说明

- **get_valid_directory**: 获取并验证用户输入的目录路径。
- **compare_directories**: 比较两个目录，生成新的文件列表和现有文件列表。
- **classify_files_by_extension**: 根据文件后缀名分类文件。
- **sort_files_by_size**: 按文件大小排序文件列表。
- **add_url_prefix**: 为文件路径添加URL前缀。
- **save_classified_files**: 将分类后的文件路径保存到输出目录的文本文件中。**output目录**：如果`output`目录不存在，则创建该目录，并将每个后缀的文件列表保存到相应的`.txt`文件中。

仓库地址：[点击前往](https://github.com/jsroads/mylibs/blob/main/upcdnScript/Python%E7%89%88%E6%9C%AC.zip)

### nodejs版本

![image-20240729131216955](./效率工具脚本-CDN预热脚本/image-20240729131216955.png)

hot-main.js 源码如下

```javascript
const fs = require('fs');
const path = require('path');

// 获取并验证用户输入的目录路径
function getValidDirectory(prompt) {
    return new Promise((resolve) => {
        const readline = require('readline').createInterface({
            input: process.stdin,
            output: process.stdout
        });

        readline.question(prompt, (inputPath) => {
            const cleanedPath = inputPath.trim().replace(/ /g, '');
            if (!fs.existsSync(cleanedPath)) {
                console.log(`路径不存在: ${cleanedPath}`);
                readline.close();
                resolve(getValidDirectory(prompt));
            } else if (!fs.lstatSync(cleanedPath).isDirectory()) {
                console.log(`这不是一个有效的目录: ${cleanedPath}`);
                readline.close();
                resolve(getValidDirectory(prompt));
            } else {
                readline.close();
                resolve(cleanedPath);
            }
        });
    });
}

// 比较目录，获取新文件和现有文件
function compareDirectories(prevDir, currDir) {
    let newFiles = [];
    let existingFiles = [];

    function walkDir(dir, callback) {
        fs.readdirSync(dir).forEach(file => {
            const fullPath = path.join(dir, file);
            if (fs.lstatSync(fullPath).isDirectory()) {
                walkDir(fullPath, callback);
            } else {
                callback(fullPath);
            }
        });
    }

    walkDir(currDir, (currFilePath) => {
        const relPath = path.relative(currDir, currFilePath);
        const prevFilePath = path.join(prevDir, relPath);
        if (!fs.existsSync(prevFilePath)) {
            newFiles.push(currFilePath);
        } else {
            existingFiles.push(currFilePath);
        }
    });

    return { newFiles, existingFiles };
}

// 按文件后缀名分类
function classifyFilesByExtension(files, extensions) {
    let classifiedFiles = {};
    extensions.forEach(ext => classifiedFiles[ext] = []);
    let otherFiles = [];

    files.forEach(file => {
        const ext = path.extname(file).toLowerCase();
        if (extensions.includes(ext)) {
            classifiedFiles[ext].push(file);
        } else {
            otherFiles.push(file);
        }
    });

    return { classifiedFiles, otherFiles };
}

// 按文件大小排序
function sortFilesBySize(files, descending = true) {
    return files.sort((a, b) => {
        const sizeA = fs.statSync(a).size;
        const sizeB = fs.statSync(b).size;
        return descending ? sizeB - sizeA : sizeA - sizeB;
    });
}

// 添加URL前缀
function addUrlPrefix(files, prefix, basePath) {
    return files.map(file => {
        const relPath = path.relative(basePath, file).replace(/\\/g, '/'); // 确保使用正斜线
        return `${prefix}/${relPath}`;
    });
}

// 保存分类后的文件列表
function saveClassifiedFiles(classifiedFiles) {
    const outputDir = 'output';
    if (fs.existsSync(outputDir)) {
        fs.rmdirSync(outputDir, { recursive: true });
        console.log(`已清空目录: ${outputDir}`);
    }
    fs.mkdirSync(outputDir);

    Object.keys(classifiedFiles).forEach(ext => {
        const filePath = path.join(outputDir, `${ext.substring(1)}.txt`);
        fs.writeFileSync(filePath, classifiedFiles[ext].join('\n'));
    });
}

async function main() {
    const previousVersionPath = await getValidDirectory("请输入上一个版本的remote目录路径: ");
    const currentVersionPath = await getValidDirectory("请输入当前要热更版本的remote目录路径: ");

    const { newFiles, existingFiles } = compareDirectories(previousVersionPath, currentVersionPath);

    const readline = require('readline').createInterface({
        input: process.stdin,
        output: process.stdout
    });

    readline.question("请输入要分类的后缀名（多个后缀用逗号分隔，直接回车统计所有后缀）: ", (extensionsInput) => {
        let extensions;
        if (extensionsInput.trim()) {
            extensions = extensionsInput.split(',').map(ext => ext.trim().toLowerCase());
            extensions = extensions.map(ext => ext.startsWith('.') ? ext : `.${ext}`);
        } else {
            extensions = Array.from(new Set(newFiles.map(file => path.extname(file).toLowerCase())));
        }

        let { classifiedFiles, otherFiles } = classifyFilesByExtension(newFiles, extensions);

        readline.question("请输入排序规则 (默认从大到小排序，输入N从小到大排序): ", (sortOrder) => {
            const descending = sortOrder.trim().toUpperCase() !== 'N';

            for (let ext in classifiedFiles) {
                classifiedFiles[ext] = sortFilesBySize(classifiedFiles[ext], descending);
            }

            readline.question("请输入要添加的URL前缀: ", (urlPrefix) => {
                for (let ext in classifiedFiles) {
                    classifiedFiles[ext] = addUrlPrefix(classifiedFiles[ext], urlPrefix, currentVersionPath);
                }

                saveClassifiedFiles(classifiedFiles);
                readline.close();
            });
        });
    });
}

main();

```

#### 使用说明

1. 使用 node hot-main.js 运行脚本

```shell
node hot-main.js
```

2. 依次输入相关的提示目录
3. 等待生成结果

```shell
Last login: Sat Jul 27 14:26:43 on ttys000
YDC012deMac-mini~/TestCase/node/hot-cdn(:|✔) % node hot-main.js
请输入上一个版本的remote目录路径: /Users/ydc012/Downloads/old
请输入当前要热更版本的remote目录路径: /Users/ydc012/Downloads/new
请输入要分类的后缀名（多个后缀用逗号分隔，直接回车统计所有后缀）:
请输入排序规则 (默认从大到小排序，输入N从小到大排序):
请输入要添加的URL前缀: https://xxxxx.com/tcs/mini/wx/publish/wechatgame_release/remote
已清空目录: output
(node:50982) [DEP0147] DeprecationWarning: In future versions of Node.js, fs.rmdir(path, { recursive: true }) will be removed. Use fs.rm(path, { recursive: true }) instead
(Use `node --trace-deprecation ...` to show where the warning was created)
YDC012deMac-mini~/TestCase/node/hot-cdn(:|✔) %
```

仓库地址：[点击前往](https://github.com/jsroads/mylibs/tree/main/upcdnScript/hot-cdn-nodejs%E7%89%88%E6%9C%AC)

## 总结

工作中，重复繁杂的工作，学会使用脚本也减轻工作，而且避免缺少步骤出错，脚本不是一下子写出来的，都是一点点学习，一点点积累，时间久了，工具会越来越完善。