---
layout: post
title: "IDEA-Maven项目的jdk版本设置"
subtitle: '在 Intellij IDEA 中，我们需要设置 Settings 中的 Java Compiler 和 Project Structure 中的 Language Level 中的 jdk 版本为自己目前使用的版本，否则会经常提示我们 jdk 版本不正确导致的语法错误。在 Maven 项目中，jdk 的配置也有一些额外需要注意的地方。'
author: "Deecyn"
catalog: true
header-style: text
tags:
  - Java
---

在 Intellij IDEA 中，我们需要设置 Settings 中的 Java Compiler 和 Project Structure 中的 Language Level 中的 jdk 版本为自己目前使用的版本，否则会经常提示我们 jdk 版本不正确导致的语法错误。在 Maven 项目中，jdk 的配置也有一些额外需要注意的地方。

比如配置为 jdk1.8 ：

![JavaCompiler](/img/java/set_idea_java_compiler.png) 

![LanguageLevel](/img/java/set_idea_language_level.png) 

但是在 Maven 项目中，Java Compiler 和 Language level 中的设置会自动变回到 pom.xml 文件中设置的 jdk 版本或者默认的 jdk1.5 版本。所以我们需要在 pom.xml 文件中修改 jdk 版本的配置或者自己添加配置：

```xml
<!-- 这里一般有 maven 的默认配置，修改即可 -->
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
 </properties>
```
或者：

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.7.0</version>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
            </configuration>
        </plugin>
    </plugins>
</build>
```

**注意：** 如果 properties 和 build 里面都有配置的话，那么 properties 会覆盖掉 build 里面的配置，即以 properties 里面的配置为准。

-----

> 如有问题，欢迎交流~ （完）