---
layout: post
title: "不同环境下jdk的配置"
subtitle: '作为 Java 程序员，jdk 的安装与配置是经常遇到的，也是新手学 Java 很可能遇到的第一个难题。经过自己多次安装配置的经验，把在 Ubuntu、Windows10 环境下的 jdk 安装配置流程记录下来，作为一篇博文，交流学习。'
author: "Deecyn"
catalog: true
header-style: text
tags:
  - Java
---

作为 Java 程序员，jdk 的安装与配置是经常遇到的，也是新手学 Java 很可能遇到的第一个难题。经过自己多次安装配置的经验，把在 Ubuntu、Windows10 环境下的 jdk 安装配置流程记录下来，作为一篇博文，交流学习。
<!--more-->

## 一、Windows系统配置Java的jdk环境
- Windows10 配置 jdk1.8

### 1.下载jdk.exe安装包
进入 [Oracle官网](http://www.oracle.com/technetwork/java/javase/downloads) 。选择系统类型及jdk的版本下载。

**PS：** 版本号的最后一位数，奇数表示为稳定版，偶数表示测试版。如：jdk-8u171 中的 171 表示为稳定版

### 2.下载完后运行exe文件安装。安装时有两次自己选择安装路径文件夹的机会：第一次是安装jdk,第二次是安装jre(也可默认安装在C盘)。选择后加载完即可.
- 选择路径时，尽量选择易辨认或记住的文件夹，且所选文件夹必须为空。
- 安装完后不要轻易改变安装路径。若改变后随之就要更改一些配置。
- 如果是第二次安装，就要更改或删除电脑 C 盘路径 C:\Program Files (x86)\Common Files\Oracle\Java或者C:\ProgramData\Oracle\Java 下的 path 文件。并在环境变量的系统变量 Path 下删除它。待下次安装完后会自动添加上去。

### 3.接下来进入环境变量界面，配置系统环境变量
- (新增或编辑)JAVA_HOME：上面安装 jdk 时选择的安装的 jdk 路径
- (新增或编辑)CLASSPATH： `.;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar` (前面有一个`.`不能漏写了)

**最后重要的一步，配置Path变量：**
- Path：`%JAVA_HOME%\bin` 、 `%JAVA_HOME%\jre\bin`
- 这里 win10 大不相同，win10 的 path 变量，要用 jdk 的绝对路径；即把 %JAVA_HOME% 替换为变量 JAVA_HOME 的路径内容，不然计算机识别不了。
- 在path环境变量的最前端加上你自己的 jdk 的这两个目录就好了
  
### 3.测试环境配置：
- 进入 cmd 窗口，输入命令java -version，显示你的jdk版本型号；
- 输入命令 javac -version，若显示你的jdk版本型号，说明环境配置成功了；
- 若显示说“java 或 javac 不是内部或外部命令”等问题，则把 cmd 窗口关闭，以管理员身份运行 cmd 再输入命令测试

再不行，就重启电脑试试。若还不行，就再自行谷歌吧。（百度多半是解决不了此问题的）


## 二、Ubuntu系统配置Java的jdk环境

- Ubuntu16.04 配置 jdk1.8
- 使用 root 用户

### 1.下载安装文件：jdk-8u201-linux-x64.tar.gz

1. 进入 [Oracle jdk 官方网站](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)

2. 点击 Accept License Agreement，选择相应的版本，点超链接

3. 到正在下载中，获取下载链接，通常类似于：
```
https://download.oracle.com/otn-pub/java/jdk/8u201-b09/42970487e3af4f5aa5bca3f542482c60/jdk-8u201-linux-x64.tar.gz?
AuthParam=1551096397_7f2e42c888c9de682d9f626d0e972f59
```

4. 其中的 AuthParam 是一个通用的参数，即使换了服务器也是可以使用的。在 Linux 等支持 wget 的主机上执行：
```
root@localhost:~# wget https://download.oracle.com/otn-pub/java/jdk/8u201-b09/42970487e3af4f5aa5bca3f542482c60/jdk-8u201-linux-x64.tar.gz?AuthParam=1551096397_7f2e42c888c9de682d9f626d0e972f59
```

5. 这个 AuthParam 是有时间限制的，超过时间限制就不能再下载啦，需要重新获取 AuthParam（重新获取下载链接）：
```
root@localhost:~# wget https://download.oracle.com/otn-pub/java/jdk/8u201-b09/42970487e3af4f5aa5bca3f542482c60/jdk-8u201-linux-x64.tar.gz?AuthParam=1551095363_20248289d791eed9ef700890d335daf3
--2019-02-25 20:01:24--  https://download.oracle.com/otn-pub/java/jdk/8u201-b09/42970487e3af4f5aa5bca3f542482c60/jdk-8u201-linux-x64.tar.gz?AuthParam=1551095363_20248289d791eed9ef700890d335daf3
Resolving download.oracle.com (download.oracle.com)... 23.44.163.29
Connecting to download.oracle.com (download.oracle.com)|23.44.163.29|:443... connected.
HTTP request sent, awaiting response... 403 Forbidden
2019-02-25 20:01:25 ERROR 403: Forbidden.
```

6. 就可以下载成功了：
```
root@localhost:~# wget https://download.oracle.com/otn-pub/java/jdk/8u201-b09/42970487e3af4f5aa5bca3f542482c60/jdk-8u201-linux-x64.tar.gz?AuthParam=1551096397_7f2e42c888c9de682d9f626d0e972f59
--2019-02-25 20:05:12--  https://download.oracle.com/otn-pub/java/jdk/8u201-b09/42970487e3af4f5aa5bca3f542482c60/jdk-8u201-linux-x64.tar.gz?AuthParam=1551096397_7f2e42c888c9de682d9f626d0e972f59
Resolving download.oracle.com (download.oracle.com)... 23.60.72.147
Connecting to download.oracle.com (download.oracle.com)|23.60.72.147|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 191817140 (183M) [application/x-gzip]
Saving to: ‘jdk-8u201-linux-x64.tar.gz?AuthParam=1551096397_7f2e42c888c9de682d9f626d0e972f59’

jdk-8u201-linux-x64 100%[===================>] 182.93M  1.88MB/s    in 1m 44s

2019-02-25 20:06:57 (1.77 MB/s) - ‘jdk-8u201-linux-x64.tar.gz?AuthParam=1551096397_7f2e42c888c9de682d9f626d0e972f59’ saved [191817140/191817140]
```


### 2.解压安装

1. cd 到文件下载的目录，查看当前下载的文件名：
```
root@localhost:~# ls
jdk-8u201-linux-x64.tar.gz?AuthParam=1551096397_7f2e42c888c9de682d9f626d0e972f59
```

2. 改名为 jdk-8u201-linux-x64.tar.gz：
```
mv jdk-8u201-linux-x64.tar.gz?AuthParam=1551096397_7f2e42c888c9de682d9f626d0e972f59 jdk-8u201-linux-x64.tar.gz
```

3. 创建目录作为JDK的安装目录，这里选择安装位置为:/usr/java/
```
mkdir /usr/java
```

4. 解压文件到 /usr/java/ 目录下:
```
tar zxvf jdk-8u201-linux-x64.tar.gz -C /usr/java/
```

5. cd 文件解压的目录，查看当前文件名：
```
root@localhost:~# cd /usr/java/
root@localhost:/usr/java# ls
jdk1.8.0_201
```

6. 改名 jdk1.8.0_201 为 jdk1.8，便于配置环境变量:
```
root@localhost:/usr/java# mv jdk1.8.0_201 jdk1.8
root@localhost:/usr/java# ls
jdk1.8
```

### 3.配置环境变量

1. 配置系统环境变量，打开环境变量文件：
```
vi /etc/environment
```

2. 填写内容：
```
PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:$JAVA_HOME/bin"

export CLASSPATH=.:$JAVA_HOME/lib:$JAVA_HOME/jre/lib

export JAVA_HOME=/usr/java/jdk1.8  # 注意此处为自己的安装路径

export JRE_HOME=${JAVA_HOME}/jre  
```

3. 配置所有用户环境变量，打开用户环境变量配置文件：
```
vi /etc/profile
```

4. 填写内容：
```
#set Java environment

JAVA_HOME=/usr/java/jdk1.8

export JRE_HOME=/usr/java/jdk1.8/jre

export CLASSPATH=.:$JAVA_HOME/lib:$JRE_HOME/lib:$CLASSPATH

export PATH=$JAVA_HOME/bin:$JRE_HOME/bin:$PATH
```

6. 设置默认jdk:
```
root@loaclhost:/# update-alternatives --install "/usr/bin/java" "java" "/usr/java/jdk1.8/bin/java" 300

root@loaclhost:/# update-alternatives --install "/usr/bin/javac" "javac" "/usr/java/jdk1.8/bin/javac" 300

root@loaclhost:/# update-alternatives --install "/usr/bin/javaws" "javaws" "/usr/java/jdk1.8/bin/javaws" 300
```

7. 检验安装成功:
```
root@localhost:/# java -version
java version "1.8.0_201"
Java(TM) SE Runtime Environment (build 1.8.0_201-b09)
Java HotSpot(TM) 64-Bit Server VM (build 25.201-b09, mixed mode)
```

-----

> 如有问题，欢迎交流~ （完）