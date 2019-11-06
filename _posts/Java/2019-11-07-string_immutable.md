---
layout: post
title: "String 的不可变性"
subtitle: 'String 类能不能被继承？为什么 String 有不可变性？这种设计有什么好处？'
author: "Deecyn"
catalog: true
header-mask: 0.1
header-img: "img/header/CivitadiBagnoregio.jpg"
tags:
  - Java
---


## 一、String 的不可变性

String 是 final 类型，final 类不能被继承。

String 是不可变的，当修改已有字符串的值的时候，（如将 `str = "abc"` 修改为 "abcdef"），不是在原内存地址上修改数据，而是将原引用重新指向新对象、新地址。为什么不可变呢？

在 jdk 1.8 的源码中，java.lang.String 类的起头几行为：

```java
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
    /** The value is used for character storage. */
    private final char value[];
    
    /** Cache the hash code for the string */
    private int hash; // Default to 0
    ...
    ...
}
```

String 本质是个 char 数组，用成员变量 value[] 存储。成员变量 value[] 用 final 关键字修饰，表示该字段创建后引用地址不可变；访问权限为 private，表示外部无法访问，成员变量 hash 也是如此。而且 String 没有对外提供可以修改这两个私有属性的方法。

String 类本身声明为 final，不可被继承，即无法通过扩展来绕过限制。

另外一点就是，构造对象时，成员变量使用 [深拷贝](https://cloud.tencent.com/developer/article/1386567) 来初始化，而不是直接复制，以防止输入对象被修改。例如，当传入可变数组 value[] 进行初始化时：

```java
public String(char value[]) {
    this.value = Arrays.copyOf(value, value.length);
}
```

## 二、这样设计的原因及好处：

其实好处就是原因，String 设计出不可变，主要是从性能和安全两方面考虑。

### 1. 字符串常量池

Java 中的字符串常量池（String pool）的存在就是为了性能优化。

字符串常量池是 Java 堆内存中一个特殊的存储区域。当创建一个 String 对象时，首先会去字符串常量池中查找，如果找到，直接返回该字符串的引用，不会创建新的对象。这样做能够减少 JVM 的内存开销，提高性能。如以下代码：

```java
String str1 = "abc";
String str2 = "abc";
```

此时引用 str1 和 str2 指向常量池中的同一个对象 "abc"，如下图所示：

![String常量池](/img/java/string_pool_example.png)

如果 String 是可变类，那么引用 str1 对 String 对象的修改，会直接导致引用 str2 获取错误的值。所以，如果字符串是可变的，那么字符串常量池就没有存在的意义了。

### 2. 缓存 HashCode

Java 中经常会用到字符串的哈希码（hashCode）。在 String 类中，有以下代码：

```java
/** Cache the hash code for the string */
private int hash; 
```

可见，成员变量 hash 中保存了一个 String 对象的 hashCode。因为 String 类不可变，所以一旦对象被创建，该 hash 值便被缓存起来，无法改变了。以后每次使用该对象的 hashCode 的时候，无需重新计算，直接返回即可。这使得字符串很适合作为 HashMap 中的 key，效率大大提高。

### 3. 多线程安全

多线程中，可变类型的值很可能在其它线程中被改变，造成不可预期的后果。而不可变的 String 可以自由在多个线程之间共享，不需要做任何同步处理。

所有，String 类的不可变性是一个精良的设计。

-----

> 如有问题，欢迎交流~