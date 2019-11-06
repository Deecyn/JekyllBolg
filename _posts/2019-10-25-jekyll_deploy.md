---
layout: post
title: "Jekyll 博客部署指南"
subtitle: '｢费力不讨好｣ 的将 Jekyll 博客部署到了阿里云服务器'
author: "Deecyn"
catalog: true
header-mask: 0.3
header-img: "img/header/jekyll-linux.jpg"
tags:
  - Jekyll
---

Jekyll 博客默认很友好的支持部署到 github-pages 上，但是在国内访问速度不是很快。这里将其部署到自己阿里云服务器上，利用 git-hooks 提交文件，一劳永逸，方便又快速。

服务器版本：Ubuntu 16.04

## 一、环境安装


Nginx 用于静态网站托管。安装 Nginx：

```bash
// 更新列表
apt-get update
apt-get install nginx
```

Git 用于版本管理和部署。安装 Git：

```bash
apt-get install git
```

## 二、托管到 Nginx

Nginx 默认的目录是 `var/www/html` ，这里在 `var/www` 下创建自己的目录 "jekyll" 进行托管。使用 root 用户创建目录：

```shell
cd ~   // 回到该用户的根目录下
mkdir var/www/jekyll
```

然后将 Nginx 默认设置的 "html" 目录改成你的 "jekyll" 目录，执行命令：

```bash
vim /etc/nginx/sites-available/default
```

编辑 default 文件，找到所有的 `root /var/www/html` 改成 `root /var/www/jekyll` ，保存退出。

然后可以将本地 Jekyll 文件夹下 _site 文件夹中的文件，通过 ftp 或 scp 的方式复制传送到 jekyll 目录下。

再重启 Nginx 服务器：`service nginx restart` ，就可以访问部署在云服务器上的博客了。

## 三、配置 Git 部署环境

### 3.1 创建 git 用户

使用 root 用户执行命令：`adduser gits` ，创建一个 git 用户名为 "gits"。为其设置密码：`passwd gits` 。

然后为这个用户配置权限。使用 root 用户授予权限，然后编辑 sudoers 文件：

```bash
$chmod 740 /etc/sudoers
$vim /etc/sudoers
```

在编辑器中找到如下内容：

```bash
# User privilege specification
root    ALL=(ALL:ALL) ALL
```

 在其下方添一行：`user_name ALL=(ALL:ALL) ALL` ，(user_name 为你的用户名，如此处为 gits）。保存退出，然后再输入 `chmod 440 /etc/sudoers` 撤回权限，并回车确认。

### 3.2 配置 git 证书

在云服务器上，为 git 用户（此处名为 gits）配置与本地环境 git 密钥相关联的证书。

- 切换到 git 用户：`su gits`
- 创建 .ssh 目录：`sudo mkdir .ssh && chmod 700 .ssh` 
- 创建 authorized_keys 公钥保存文件：`touch .ssh/authorized_keys && chmod 600 .ssh/authorized_keys` 
- 编辑 authorized_keys 文件，将本地 git 的公钥 id_rsa.pub 文件中的内容添加进去。

### 3.3 创建 git 仓库目录并配置 git hooks

使用 root 用户，创建一个名为 jekyll 的 git 仓库：

```bash
mkdir /var/repo
cd /var/repo
git init --bare jekyll.git
```

执行以下命令，编辑 post-receive 文件：

```bash
cd ~
vim /var/repo/jekyll.git/hooks/post-receive
```

为配置 git hooks，向 post-receive 文件添加以下内容：

```bash
#!/bin/sh
git --work-tree=/var/www/jekyll --git-dir=/var/repo/jekyll.git checkout -f
```

**注：**`/var/www/jekyll` 为前面 Nginx 服务器中配置的静态文件目录。

保存退出后，设置权限：

```bash
chmod +x /var/repo/jekyll.git/hooks/post-receive
```

改变 jekyll.git 目录的拥有者为 git 用户（gits）：

```bash
chown -R gits:gits jekyll.git
```

将 git 仓库链接到 Nginx 服务器的静态文件目录，并设置权限：

```bash
chown -R gits:gits /var/www/jekyll
chmod -R 755 /var/www/jekyll
```

 这样，git 仓库更新便会自动同步到 jekyll目录下。

### 3.4 禁用 git 用户的 shell 登录权限

 首先必须确保 git-shell 已存在于 /etc/shells 文件中：

- 使用命令 `which git-shell` 判断系统是否安装了git-shell。如果已经安装，则返回 git-shell 的安装目录，如：/usr/bin/git-shell；如果未安装则需要安装 git-shell 命令，安装命令：`apt-get install git`
-  判断 shells 文件是否存在，判断命令：`cat /etc/shells` 
-  如果文件不存在或没有 `/usr/bin/git-shell`，则需要使用 vim 增加这个路径：
  `sudo vim /etc/shells`，在最后一行添加 git-shell 路径：

```bash
# /etc/shells: valid login shells 
/bin/sh
/bin/dash
/bin/bash
/bin/rbash
/usr/bin/tmux
/usr/bin/screen
/usr/bin/git-shell # 添加你的 git-shell
```

现在便可以使用 chsh 命令修改任一系统用户的 shell 权限了。这里我们禁止 git 用户（gits）使用 shell 权限，在终端中输入 `sudo chsh gits` ， 然后在 `Login Shell [/bin/bash]` ：后输入 git-shell 路径 `/usr/bin/git-shell` ，回车确认。

 **修改完成后验证：** `vim /etc/passwd` 找到类似 `gits:x:1000:1000:,,,:/home/gits:/usr/bin/git-shell` ，看看用户 gits 是否是以 git-shell 结尾 。

 这样，用户 gits 就只能使用 SSH 连接对 Git 仓库进行推送和拉取操作，而不能登录机器并取得普通 shell 命令。 

### 3.5 测试

当上述步骤都完成后，我们就可以测试下 gi t服务器是否部署成功，最简单的方法便是使用 clone 来校验。

用户电脑（window or mac）git客户端执行 clone 操作： `git clone gits@服务器ip:/var/repo/jekyll.git` ；输入创建 gits 这个用户的密码， 如果 clone 成功，表示 git 服务器搭建成功。 

然后，可以将 jekyll 博客生成的 _site 文件夹下的文件复制出来，利用 git 上传到云服务器，然后访问云服务器上托管在 Nginx 中的静态网站即可。

-----

> 如有问题，欢迎交流~ （完）

