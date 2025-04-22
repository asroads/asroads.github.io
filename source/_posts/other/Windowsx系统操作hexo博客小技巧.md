---
title: Windowsx系统操作hexo博客小技巧
comments: true
categories: other
tags:
  - hexo
  - 技术
abbrlink: a82fa4c7
date: 2025-01-01 13:13:53
---

最近`Mac`电脑交回原来的公司了，只能使用`Windows`电脑学习和娱乐了，于是想着把自己的个人博客在电脑上布置一下，以前一直在Mac电脑上操作这些都很熟悉，转回`Windows`系统后，遇到了一些小问题，于是记录一下吧，方便以后查阅。
<!--more-->

## 系统操作

### 操作命令转换

1. 以前用Mac电脑习惯了 `ls` 命令和 `open .` 命令，换回Windows 系统突然要转换，在 Windows 系统中，虽然没有直接的 `ls` 和 `open .` 命令，但有一些等效的操作可以帮助你快速适应：

#### 等效命令：

1. **`ls` 等效**：

   - 使用 `dir` 命令：

     ```bash
     dir
     ```

     它会列出当前目录的文件和文件夹内容，但显示格式略有不同。

2. **`open .` 等效**：

   - 使用 

     ```bash
     explorer .
     ```

     这个命令会用文件资源管理器打开当前目录。

3.  快捷键清屏

   - 使用 

     ```bash
     cls
     ```

     这是 Linux/Unix 系统的清屏命令，在支持这些命令的环境中也可以使用。

#### 使用 Git Bash：

- 如果你习惯于 `Linux` 命令，可以安装 `Git for Windows`，自带的 `Git Bash` 环境支持 `ls` 和 `open .`这样设置后，你可以在 `Windows` 中使用与 `Mac/Linux` 类似的命令习惯，过渡更加流畅！

### git fetch和git pull 报错

```bash
D:\asroads\blog\asroads.github.io>git pull
fatal: unable to access 'https://github.com/asroads/asroads.github.io.git/': Failed to connect to github.com port 443 after 21044 ms: Couldn't connect to server

D:\asroads\blog\asroads.github.io>git fetch --all
error: RPC failed; curl 28 Recv failure: Connection was reset
fatal: expected flush after ref listing
```

#### git fetch --all 报错 修复

```bash
git config --global http.postBuffer 524288000
```

#### git pull 修复

```bash
git config --global --unset http.proxy
git config --global --unset https.proxy
```

#### 使用ping 命令验证

```bash
D:\asroads\blog\asroads.github.io>ping github.com

正在 Ping github.com [20.205.243.166] 具有 32 字节的数据:
来自 20.205.243.166 的回复: 字节=32 时间=95ms TTL=108
来自 20.205.243.166 的回复: 字节=32 时间=97ms TTL=108
来自 20.205.243.166 的回复: 字节=32 时间=95ms TTL=108
来自 20.205.243.166 的回复: 字节=32 时间=98ms TTL=108

20.205.243.166 的 Ping 统计信息:
    数据包: 已发送 = 4，已接收 = 4，丢失 = 0 (0% 丢失)，
往返行程的估计时间(以毫秒为单位):
    最短 = 95ms，最长 = 98ms，平均 = 96ms
```

## hexo全局安装以及配置

```bash
npm install hexo-cli -g
```

### hexo 推送报错

`hexo d` 命令执行的时候报错

```bash
The authenticity of host 'github.com (20.205.243.166)' can't be established.
ED25519 key fingerprint is SHA256:+DiY3wvvV6TuJJhbpZisF/zLDA0zPMSvHdkr4UvCOqU.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'github.com' (ED25519) to the list of known hosts.
git@github.com: Permission denied (publickey).
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
FATAL Something's wrong. Maybe you can find the solution here: https://hexo.io/docs/troubleshooting.html
Error: Spawn failed
    at ChildProcess.<anonymous> (D:\asroads\blog\asroads.github.io\node_modules\hexo-deployer-git\node_modules\hexo-util\lib\spawn.js:51:21)
    at ChildProcess.emit (node:events:513:28)
    at ChildProcess.cp.emit (D:\asroads\blog\asroads.github.io\node_modules\cross-spawn\lib\enoent.js:34:29)
    at Process.ChildProcess._handle.onexit (node:internal/child_process:293:12)
```

抱错 具体原因是 电脑新环境没有公钥生成

解决方案：

```bash
git config --global user.name "your github user name"
git config --global user.email "your github 注册邮箱"
```

代码执行后，生成公钥

```bash
ssh-keygen -t rsa -b 4096 -C "asroads@163.com"
```

```bash
D:\asroads\blog\asroads.github.io>ssh-keygen -t rsa -b 4096 -C "asroads@163.com"
Generating public/private rsa key pair.
Enter file in which to save the key (C:\Users\jsroads/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in C:\Users\jsroads/.ssh/id_rsa
Your public key has been saved in C:\Users\jsroads/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:QYaCbOfHyQ1JI21bpSwHmYzKqasVsovQJIsT24gLWqY asroads@163.com
The key's randomart image is:
+---[RSA 4096]----+
| . ..=+=o..      |
|  + +.X*..       |
| o = =.*=        |
|  + . *o..       |
|+.o  .  S        |
|+% .             |
|@.*              |
|*O               |
|E                |
+----[SHA256]-----+

D:\asroads\blog\asroads.github.io>
```

### 参数说明：

- **`-t rsa`**：指定生成 RSA 类型的密钥。
- **`-b 4096`**：设置密钥长度为 4096 位（可选，默认是 2048）。
- **`-C "asroads@163.com"`**：为密钥添加注释，通常是你的邮箱地址，便于区分。

### 命令执行流程：

1. 执行命令后，系统会提示你保存密钥文件的位置：

   - 默认路径是：`C:\Users\<用户名>\.ssh\id_rsa`
   - 按回车保存到默认路径，或输入自定义路径。

2. 然后会提示输入密码（可选）：

   - 如果你希望密钥更安全，可以设置一个密码。
   - 如果不需要密码保护，直接按回车跳过。

3. 成功生成后，你会看到类似以下输出：

   ```
   Your identification has been saved in C:\Users\<用户名>\.ssh\id_rsa
   Your public key has been saved in C:\Users\<用户名>\.ssh\id_rsa.pub
   ```

### 验证生成的密钥：

查看 `.ssh` 文件夹中是否存在密钥文件：

```bash
dir C:\Users\<用户名>\.ssh
```

应该包含以下两个文件：

- `id_rsa`：私钥
- `id_rsa.pub`：公钥

### 将公钥添加到 GitHub：

1. 打开 `id_rsa.pub` 文件，复制其中的内容。
2. 登录 GitHub，进入 **Settings** > **SSH and GPG keys** > **New SSH key**。
3. 粘贴公钥，添加后保存。

### 测试 SSH 连接：

执行以下命令，测试是否成功连接到 GitHub：

```bash
ssh -T git@github.com
```

成功的话会输出类似以下内容：

```bash
D:\asroads\blog\asroads.github.io>ssh -T git@github.com
Hi asroads! You've successfully authenticated, but GitHub does not provide shell access.
```

### 再次推送成功

```
 4 files changed, 63 insertions(+), 10 deletions(-)
Enumerating objects: 1897, done.
Counting objects: 100% (1897/1897), done.
Delta compression using up to 8 threads
Compressing objects: 100% (1712/1712), done.
Writing objects: 100% (1897/1897), 45.02 MiB | 1.86 MiB/s, done.
Total 1897 (delta 405), reused 0 (delta 0), pack-reused 0 (from 0)
remote: Resolving deltas: 100% (405/405), done.
To github.com:asroads/asroads.github.io.git
 + b21d2c5...365632b HEAD -> master (forced update)
branch 'master' set up to track 'git@github.com:asroads/asroads.github.io.git/master'.
INFO  Deploy done: git

D:\asroads\blog\asroads.github.io>
```

## git 推送远端失败

```bash
D:\asroads\blog\asroads.github.io>git push origin back
fatal: unable to access 'https://github.com/asroads/asroads.github.io.git/': OpenSSL SSL_read: SSL_ERROR_SYSCALL, errno 0

D:\asroads\blog\asroads.github.io>ping github.com

正在 Ping github.com [20.205.243.166] 具有 32 字节的数据:
来自 20.205.243.166 的回复: 字节=32 时间=96ms TTL=108
来自 20.205.243.166 的回复: 字节=32 时间=96ms TTL=108
来自 20.205.243.166 的回复: 字节=32 时间=96ms TTL=108
来自 20.205.243.166 的回复: 字节=32 时间=95ms TTL=108

20.205.243.166 的 Ping 统计信息:
    数据包: 已发送 = 4，已接收 = 4，丢失 = 0 (0% 丢失)，
往返行程的估计时间(以毫秒为单位):
    最短 = 95ms，最长 = 96ms，平均 = 95ms

D:\asroads\blog\asroads.github.io
```

**方法一：改用 SSH 协议**

HTTPS 遇到问题时，可以切换到 SSH 协议：

1. **切换远程仓库 URL 到 SSH**：

   ```bash
   git remote set-url origin git@github.com:asroads/asroads.github.io.git
   ```

2. **测试 SSH 连接**：

   ```bash
   ssh -T git@github.com
   ```

3. **推送代码**：

   ```bash
   git push origin back
   ```

**方法二强制使用 HTTP/1.1**

某些网络环境下，Git 的 HTTP/2 协议可能会导致问题。可以尝试强制使用 HTTP/1.1：

```bash
git config --global http.version HTTP/1.1
```

## 总结

总的来说每次博客或者更换系统环境多少都会遇到问题，基本可以归结为两种问题，一种是权限问题，另外一种就是网络问题。遇到这些问题，可以简单的搜索一下，基本这些问题都能找到对应的解决方法。
