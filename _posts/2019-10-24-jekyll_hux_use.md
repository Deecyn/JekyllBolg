---
layout: post
title: "关于 Jekyll 博客 hux 主题的使用"
subtitle: 'About the use of the Huky theme on the Jekyll blog.'
author: "Deecyn"
header-style: text
tags:
  - Jekyll
---



## 关于样式配置



## 关于文章

文章的 Markdown 文件可以在 _post 文件夹的子目录下，但是文件名应该以 `2019-10-24-` 这种格式的日期开头，且名称要为英文。

文章的头部配置样例：

```markdown
---
layout:       post  
title:        "饿了么的 PWA 升级实践"
subtitle:     'Upgrading Ele.me to Progressive Web App'
author:       "Deecyn"
// header-style: text
header-img:   "img/in-post/post-eleme-pwa/eleme-at-io.jpg"
header-mask:  0.3
catalog:      true
multilingual: true
tags:
    - Web
    - PWA
---
```

配置参数描述：

```java
参数：          样例：                  // 描述：

layout:     keynote                   // 头部显示html幻灯片
iframe:     "http://huangxuan.me/js-module-7day/"  // html幻灯片资源链接

layout:       post                    // 布局方式
title:        "Java 与 Spring"        // 文章标题
subtitle:     "看看这些联系与应用"       // 副标题，展示列表显示的文本，或者标题图上显示的内容
author:       "Deecyn"                // 作者名字
header-style: text                    // 文章头部显示为文本

header-img:   "img/java_logo.jpg"     // 文章头部显示图片，并配置图片路径;
                                      // 图片上的文字为白色，所以图片尽量为深色。
header-mask:  0.3                     // 头部图片的灰暗度，越接近 1 越黑
catalog:      true                    // 是否侧面显示目录
multilingual: true                    // 是否多种语言
mathjax:      true                    // 是否展示数学公式语法
tags:
    - Java
    - Spring
```





## 关于部署

