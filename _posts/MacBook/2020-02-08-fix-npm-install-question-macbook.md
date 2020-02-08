---
layout: post
title: "MacBook 中 npm install 的问题"
subtitle: '在 MacBook 的系统升级到 macOS Catalina 10.15.3 的情况下，使用 NodeJS 中的 npm install 命令的时候遇到了一些让人摸不着头脑的问题（对于一个没怎么学过前端的人）。'
author: "Deecyn"
catalog: true
header-style: text
tags:
  - MacBook
  - NodeJS
---



例如使用 `npm install` 命令全局安装 hexo 时，会遇到以下问题： 

## 1.

报错信息：

```shell
wdx-mac:HexoBlog wdx$ npm i -g hexo
npm WARN checkPermissions Missing write access to /usr/local/lib/node_modules/hexo
npm WARN checkPermissions Missing write access to /usr/local/lib/node_modules/hexo/node_modules
npm WARN checkPermissions Missing write access to /usr/local/lib/node_modules/hexo/node_modules/nunjucks/node_modules
npm WARN checkPermissions Missing write access to /usr/local/lib/node_modules
npm ERR! code EACCES
npm ERR! syscall access
npm ERR! path /usr/local/lib/node_modules/hexo
npm ERR! errno -13
npm ERR! Error: EACCES: permission denied, access '/usr/local/lib/node_modules/hexo'
npm ERR!  [Error: EACCES: permission denied, access '/usr/local/lib/node_modules/hexo'] {
npm ERR!   stack: "Error: EACCES: permission denied, access '/usr/local/lib/node_modules/hexo'",
npm ERR!   errno: -13,
npm ERR!   code: 'EACCES',
npm ERR!   syscall: 'access',
npm ERR!   path: '/usr/local/lib/node_modules/hexo'
npm ERR! }
npm ERR!
npm ERR! The operation was rejected by your operating system.
npm ERR! It is likely you do not have the permissions to access this file as the current user
npm ERR!
npm ERR! If you believe this might be a permissions issue, please double-check the
npm ERR! permissions of the file and its containing directories, or try running
npm ERR! the command again as root/Administrator.

npm ERR! A complete log of this run can be found in:
npm ERR!     /Users/wdx/.npm/_logs/2020-02-07T14_40_22_599Z-debug.log
```

**解决办法：** npm 的安装权限不够。在安装命令前面加上 sudo，赋予 root 权限，按提示输入密码即可。

## 2.

报错信息：

```shell
wdx-mac:HexoBlog wdx$ sudo npm i -g hexo
Password:
/usr/local/bin/hexo -> /usr/local/lib/node_modules/hexo/bin/hexo

> fsevents@1.2.11 install /usr/local/lib/node_modules/hexo/node_modules/nunjucks/node_modules/fsevents
> node-gyp rebuild

gyp WARN EACCES current user ("nobody") does not have permission to access the dev dir "/Users/wdx/Library/Caches/node-gyp/12.15.0"
gyp WARN EACCES attempting to reinstall using temporary dev dir "/usr/local/lib/node_modules/hexo/node_modules/nunjucks/node_modules/fsevents/.node-gyp"
gyp WARN install got an error, rolling back install
gyp WARN install got an error, rolling back install
gyp ERR! configure error
gyp ERR! stack Error: EACCES: permission denied, mkdir '/usr/local/lib/node_modules/hexo/node_modules/nunjucks/node_modules/fsevents/.node-gyp'
gyp ERR! System Darwin 19.3.0
gyp ERR! command "/usr/local/bin/node" "/usr/local/lib/node_modules/npm/node_modules/node-gyp/bin/node-gyp.js" "rebuild"
gyp ERR! cwd /usr/local/lib/node_modules/hexo/node_modules/nunjucks/node_modules/fsevents
gyp ERR! node -v v12.15.0
gyp ERR! node-gyp -v v5.0.5
gyp ERR! not ok
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@1.2.11 (node_modules/hexo/node_modules/nunjucks/node_modules/fsevents):
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@1.2.11 install: `node-gyp rebuild`
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: Exit status 1
```

**解决方法：** 仍然是权限不够的问题，在安装命令后面加上参数 ` --unsafe-perm` 即可。

- 参考链接：[root权限NPM全局安装(-g)仍会权限不够，认识下参数 --unsafe-perm](https://www.cnblogs.com/ganiks/p/stack-error-eacces-denied-node-gyp.html)

## 3.

报错信息：

```shell
wdx-mac:HexoBlog wdx$ sudo npm i -g hexo --unsafe-perm
/usr/local/bin/hexo -> /usr/local/lib/node_modules/hexo/bin/hexo

> fsevents@1.2.11 install /usr/local/lib/node_modules/hexo/node_modules/nunjucks/node_modules/fsevents
> node-gyp rebuild

No receipt for 'com.apple.pkg.CLTools_Executables' found at '/'.

No receipt for 'com.apple.pkg.DeveloperToolsCLILeo' found at '/'.

No receipt for 'com.apple.pkg.DeveloperToolsCLI' found at '/'.

gyp: No Xcode or CLT version detected!
gyp ERR! configure error
gyp ERR! stack Error: `gyp` failed with exit code: 1
gyp ERR! stack     at ChildProcess.onCpExit (/usr/local/lib/node_modules/npm/node_modules/node-gyp/lib/configure.js:351:16)
gyp ERR! stack     at ChildProcess.emit (events.js:223:5)
gyp ERR! stack     at Process.ChildProcess._handle.onexit (internal/child_process.js:272:12)
gyp ERR! System Darwin 19.3.0
gyp ERR! command "/usr/local/bin/node" "/usr/local/lib/node_modules/npm/node_modules/node-gyp/bin/node-gyp.js" "rebuild"
gyp ERR! cwd /usr/local/lib/node_modules/hexo/node_modules/nunjucks/node_modules/fsevents
gyp ERR! node -v v12.15.0
gyp ERR! node-gyp -v v5.0.5
gyp ERR! not ok
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@1.2.11 (node_modules/hexo/node_modules/nunjucks/node_modules/fsevents):
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@1.2.11 install: `node-gyp rebuild`
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: Exit status 1
```

**解决方法：** 需要安装 XCode 命令行工具（XCode Command Line Tools）：

```
xcode-select --install
```

若提示已安装，则需要删除原有的：

```
sudo rm -rf /Library/Developer/CommandLineTools

# 或：sudo rm -rf $(xcode-select -print-path)
```

然后重新安装 XCode 命令行工具。然后再试 npm install 命令即可。

- 参考链接：[修复 Mac 下 npm install 与 Xcode 版本的问题](https://cleody.com/fix-no-xcode-or-clt-version-detected-when-running-npm-install/)

-----

（完）