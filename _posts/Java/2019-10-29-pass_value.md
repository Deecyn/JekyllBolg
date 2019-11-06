---
layout: post
title: "Java 与值传递"
subtitle: '什么是值传递？什么是引用传递？为什么说 Java 中只有值传递？'
author: "Deecyn"
catalog: true
header-mask: 0.1
header-img: "img/header/starry _sky.jpg"
tags:
  - Java
---

## 一、值传递与引用传递

实参与形参：

- 实际参数：在调用有参函数时，主调函数和被调函数之间有数据传递关系。在主调函数中调用被调函数时，函数名后面括号中的参数称为“实际参数”。
- 形式参数：在定义函数名和函数体时使用的参数，目的是用来接收调用此函数时传入的参数。

值传递与引用传递：

- 值传递：是指在调用函数时，将实际参数复制一份，通过形参传递到函数中。这时形参接收到的内容是实参的一个副本，这样在函数中如果对形参进行修改，不会影响到实际参数。
- 引用传递：是指在调用函数时，将实际参数的地址传递到函数中，那么在函数中对参数进行的修改，将会影响到实际参数。

重点的区别在于：

|    方面    | 值传递                 | 引用传递               |
| :--------: | ---------------------- | ---------------------- |
| 根本区别： | 会创建副本（copy）     | 不会创建副本           |
|   所以：   | 函数中无法改变原始对象 | 函数中可以改变原始对象 |

## 二、Java 中的值传递

这里需要清楚 JVM 内存的划分及职能，即：
1. 虚拟机栈
2. 堆
3. 程序计数器
4. 方法区
5. 本地方法栈

已知，JVM 会给每个线程分配一个 Java 栈，当执行一个方法时，JVM 会往该栈中压入一个栈帧。

### 1. 对于基本数据类型

在方法中，基本数据类型的形参及变量（称为局部变量）的名字和值都存储于栈中。在该方法被调用时，会为形参在栈帧中开辟一块内存，将实参值复制给形参，然后形参和实参之间便没有了关联。

**即，此后在方法中对形参的操作不会影响到原来的实参。** 例：

```java
public static void main(String[] args) {
    int a = 21;
    intTest(a);
    System.out.println("方法执行后的a: "+a);
}

public static void intTest(int var){
    // 将实参值复制给形参
    System.out.println("传入的形参var："+var);
    
    // 在方法中对形参的操作不会影响到原来的实参
    var = 23;
    System.out.println("方法内重新赋值后的形参var："+var);
}
```

运行结果：

```
传入的形参var：21
方法内重新赋值后的形参var：23
方法执行后的a: 21
```

### 2. 对于引用数据类型（对象类型）

Java 里面的变量，要么是基本数据类型，要么是指向对象实例的引用类型，绝对不会是一个对象。

对于引用数据类型的对象，变量的名和值均存储在栈中，变量值存储的是对象的地址，对象的实际内容存储于堆（堆区是共享的）中。在该方法被调用时，会为形参在栈帧中开辟一块内存，将实参值复制给形参，即把实参指向的对象的地址复制给了形参：

1.此后在方法内部，如果没有改变形参值（对象的地址），那么可以通过该形参操作原实参指向的堆中的对象。这样虽然原对象的属性可能发生变化，但原实参的值（指向的地址）并没有发生变化。例：

```java
// 设 Student 类对象有 name 和 age 两个属性

public static void main(String[] args) {
    Student student = new Student("张三",18);
    System.out.println("原对象的hashCode值："+student.hashCode());
    referenceTest(student);

    System.out.println("测试后的原对象："+student);
    System.out.println("测试后原对象的hashCode值："+student.hashCode());
}

public static void referenceTest(Student stu){
    System.out.println("改变前形参指向对象的hashCode值："+stu.hashCode());

    stu.setName("李四");
    System.out.println("改变后形参指向的对象："+stu);
    System.out.println("改变后形参指向对象的hashCode值："+stu.hashCode());
}
```

运行结果：（这里 hashCode 值可代表地址）

```java
原对象的hashCode值：21685669

改变前形参指向对象的hashCode值：21685669
改变后形参指向的对象：Student{name='李四', age=18}
改变后形参指向对象的hashCode值：21685669

测试后的原对象：Student{name='李四', age=18}
测试后原对象的hashCode值：21685669
```

2.如果该形参指向其它对象，或者指向新 new 的对象，那么形参值变为新对象的内存地址。此后通过该形参操作的会是新的对象，原实参的值（指向的地址）不会发生变化。例：

```java
// 设 Student 类对象有 name 和 age 两个属性

public static void main(String[] args) {
    Student student = new Student("张三",18);
    System.out.println("原对象的hashCode值："+student.hashCode());
    referenceTest(student);

    System.out.println("测试后的原对象："+student);
    System.out.println("测试后原对象的hashCode值："+student.hashCode());
}

public static void referenceTest(Student stu){
    System.out.println("改变前形参指向对象的hashCode值："+stu.hashCode());

    stu = new Student("王五",21);
    System.out.println("改变后形参指向的对象："+stu);
    System.out.println("改变后形参指向对象的hashCode值："+stu.hashCode());
}
```

运行结果：（这里 hashCode 值可代表地址）

```java
原对象的hashCode值：21685669

改变前形参指向对象的hashCode值：21685669
改变后形参指向的对象：Student{name='王五', age=21}
改变后形参指向对象的hashCode值：2133927002  // 有变化

测试后的原对象：Student{name='张三', age=18}
测试后原对象的hashCode值：21685669
```

**即，此后在方法中对形参的操作并不会影响到原来的实参值（原对象的地址）。**

-----

参考博文：
- https://mp.weixin.qq.com/s/F7Niaa7nD1tLApCEGKAj4A
- https://juejin.im/post/5bce68226fb9a05ce46a0476
- https://zhuanlan.zhihu.com/p/29074454



-----

> 如有问题，欢迎交流~ （完）