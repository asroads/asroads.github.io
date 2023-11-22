---
title: 最近写的一个下载和复制的Python脚本
comments: true
categories: tool
tags:
  - Python
abbrlink: 2c94d2d6
date: 2023-10-07 20:00:00
---

有时候项目需要一些批量处理的脚本，最近就遇到了一些需求，于是记下来，后面用到的时候直接拿来使用方便简单。这里是两个功能，一个是从网上下载一批文件，保存到本地的目录，另外一个是批量复制并且覆盖原来的文件。
<!--more-->

## 批量下载

由于脚本不太复杂，这里直接给出脚本

```python
import os
import requests
import sys
import urllib.parse

def download_files_from_list(file_path, root_dir, split_index=1):
    # 确保根目录存在
    if not os.path.exists(root_dir):
        os.makedirs(root_dir)
    
    # 打开文件并逐行读取URL
    with open(file_path, 'r', encoding='utf-8') as f:
        for line in f:
            url = line.strip()
            # 使用指定的索引分割URL，以获取路径
            decoded_url = urllib.parse.unquote(url)
            # 现在path_from_url使用解码后的URL
            path_from_url = "/".join(decoded_url.split("/")[split_index:])
            # path_from_url = "/".join(url.split("/")[split_index:])
            # 创建本地保存路径
            local_path = os.path.join(root_dir, path_from_url)
            local_dir = os.path.dirname(local_path)
            
            # 确保本地目录存在
            if not os.path.exists(local_dir):
                os.makedirs(local_dir)
            
            # 下载文件并保存到本地
            response = requests.get(url)
            with open(local_path, 'wb') as out_file:
                out_file.write(response.content)
            print(f"Downloaded {url} to {local_path}")

if __name__ == "__main__":
    if len(sys.argv) < 3:
        print("Usage: python down.py <file_path> <root_dir> [split_index]")
        sys.exit(1)
    
    file_path = sys.argv[1]
    root_dir = sys.argv[2]
    split_index = int(sys.argv[3]) if len(sys.argv) == 4 else 1
    download_files_from_list(file_path, root_dir, split_index)

```

### 解释

这里主要解释一下：

```python
   # 使用指定的索引分割URL，以获取路径
   decoded_url = urllib.parse.unquote(url)
   # 现在path_from_url使用解码后的URL
   path_from_url = "/".join(decoded_url.split("/")[split_index:])
   # path_from_url = "/".join(url.split("/")[split_index:])

```

由于有时候我们下载的文件内容 可能是 `%E7%81%AB%E8%BD%A6%E9%B8%A3%E7%AC%9B`此时我们需要进行一次转码操作，把这个转成正确的显示比如 这里应该是 `火车鸣笛`

这些URL中的中文部分使用的是“百分号编码”（Percent-Encoding），这是一种在URI（统一资源标识符）中表示特殊字符的方法。在这种编码中，非ASCII字符（如中文字符）会被转换成百分号（%）后跟两位十六进制数。这种编码允许将字符表示为URL可以理解的格式。

例如，中文字符“火”被编码为 `%E7%81%AB`。这里，“E7”，“81”，和“AB”是字符“火”在UTF-8编码下的十六进制字节表示。

要对这些URL进行解码，可以使用Python中的 `urllib.parse.unquote` 函数，它会自动将百分号编码转换回原始字符。

#### 额外的正则替换

这里是我自己这边独有的，比如我的文件 需要批量替换一些文本，比如我这里需要替换 逗号 file 开头 0000 UTC 结尾的所有的内容 

```javascript
, file.*?0000 UTC
```

### 调用

```shell
python downFile.py ./diZhiTest1.txt  ./out 4
```

## 批量复制覆盖

```python
import os
import shutil
import sys
import ast

def replace_files_from_dir(source_dir, target_dir, extensions):
    # 遍历目标目录
    for root, _, files in os.walk(target_dir):
        for file in files:
            # 只处理指定后缀的文件
            if any(file.endswith(ext) for ext in extensions):
                # 检查源目录中是否存在同名文件
                source_file_path = os.path.join(source_dir, file)
                if os.path.exists(source_file_path):
                    target_file_path = os.path.join(root, file)
                    # 使用源文件替换目标文件
                    shutil.copy(source_file_path, target_file_path)
                    print(f"Replaced {target_file_path} with {source_file_path}")

if __name__ == "__main__":
    if len(sys.argv) != 4:
        print("Usage: python replace.py <source_dir> <target_dir> <extensions>")
        sys.exit(1)
    
    source_dir = sys.argv[1]
    target_dir = sys.argv[2]
    # 使用 ast.literal_eval 安全地将字符串转换为列表
    extensions = ast.literal_eval(sys.argv[3])
    replace_files_from_dir(source_dir, target_dir, extensions)

```

### 调用

```shell
python3 replace.py /Users/ydc012/Documents/FilesCopy/replaceFiles/替换测试/压缩后 /Users/ydc012/Documents/FilesCopy/replaceFiles/替换测试/remote "['.png','.jpg']"
```

第三个参数是 复制文件的后缀。

## 其他拓展

这里再说一个python 里面关于 https地址的替换，我这里是实现时 构建后修改资源目录 `settings.js` 下面的 `server` 属性

```python
    # 修改 settings.js
    # 替换 <channel> 和 <mode> 以构建 cdn_url
    cdn_url = os.path.join(CDN_URL.replace('<channel>',CHANNEL).replace('<mode>',GAME_MODE),folder)
    print("远程CDN目录",cdn_url)
    print("SETTINGS_FILE",SETTINGS_FILE)
    with open(SETTINGS_FILE, 'r') as file:
        settings_content = file.read()
    # 用于匹配文件中的server属性的正则表达式
    pattern = r'(server\s*:\s*")[^"]*(")'
    # 执行替换操作
    new_content = re.sub(pattern, r'\1' + cdn_url + r'\2', settings_content)
    with open(SETTINGS_FILE, 'w') as f:
        f.write(new_content)
```

核心代码：

```python
    # 用于匹配文件中的server属性的正则表达式
    pattern = r'(server\s*:\s*")[^"]*(")'
    # 执行替换操作
    new_content = re.sub(pattern, r'\1' + cdn_url + r'\2', settings_content)
```

