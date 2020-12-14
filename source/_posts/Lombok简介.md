---
title: Lombok简介
translate_title: introduction-to-lombok
date: 2020-04-13 19:36:38
categories:
tags:
---

大家最近都在讨论的Lombok到底是什么？

第一次接触Lombok是在实习的时候知道的，当时我还在想这么方便？后来我发现了新世界的大门。

从直观的效果来看，一个简单的Java对象在没有使用Lombok之前的代码：

![image-20200413194418866](D:\Program Files\hexo\blog\source\_posts\Lombok简介\1.png)

使用Lombok之后：

![image-20200413194529961](D:\Program Files\hexo\blog\source\_posts\Lombok简介\2.png)

使用`@Data`注解之后，从直观上看缩短了Java对象的代码量。那么Lombok是什么？

![image-20200413195419633](D:\Program Files\hexo\blog\source\_posts\Lombok简介\4.png)

Lombok项目是一个Java库，它会自动插入您的编辑器和构建工具中，从而为您的Java增光添彩。
永远不要再编写另一个getter或equals方法，带有一个注释的类将具有功能全面的生成器，自动执行日志记录变量等等。

常见的Lombok注解有：

![image-20200413195319047](D:\Program Files\hexo\blog\source\_posts\Lombok简介\3.png)

![5](D:\Program Files\hexo\blog\source\_posts\Lombok简介\5.png)

`@Getter、@Setter、@ToString、@EqualsAndHashCode`这四个注解的总和也就是刚开始用的注解 `@Data`

`@AllArgsConstructor、@RequiredArgsConstructor、@NoArgsConstructor`分别为全参构造函数、必须参数构造函数、无参构造函数，它们通常为构造方法的注解。

 `@NonNull` 会自动生成空值校验。

`@CleanUp` 会自动调用变量的 `close` 方法释放资源。

`@Builder` 会自动生成构造者模式，方便对属性 `set/get` 操作。

`@Synchronized` 会自动生成同步锁。

`@SneakyThrows` 会自动生成 `try/catch` 捕捉异常。

`@Slf4j`是日志相关的，会自动为类添加日志支持。

这些都是我们平时常用的Lombok注解。

那么Lombok是如何实现的：

注解有两种解析方式，分别是运行时注解和编译时注解。

**运行时解析**，比如 Spring 配置的 AOP 切面这些注解都是在程序运行的时候通过反射来获取的注解值，但是只有在程序运行时才能获取到这些注解值，导致运行时代码效率很低，并且如果想在编译阶段利用这些注解来进行检查，比如对用户的不合理代码作出错误报告，反射的方法就行不通了。

**编译时解析**，Lombok 工具就是运行在编译时解析的。

那如何把注解与 Java 编译器结合使用呢？Java 也提供了两种解决方案：

第一种方案是**注解处理器（Annotation Processing Tool）**，它最早是在 JDK 1.5 与**注解（Annotation）** 一起引入的，它是一个命令行工具，能够提供构建时基于源代码对程序结构的读取功能，能够通过运行注解处理器来生成新的中间文件，进而影响编译过程，不过它在 JDK 1.8 中被移除了，取而代之的是 **JSR 269 插入式注解处理器（Pluggable Annotation Processing API）**，它是实现了 **JSR 269** 的机制，作为注解处理器的替代方案。

通过一个流程图来进一步说明注解处理器的工作原理：

<img src="D:\Program Files\hexo\blog\source\_posts\Lombok简介\6.png" alt="6" style="zoom: 33%;" />

首先调用 **`javac` 编译**，在编译后会**生成抽象语法树（AST）**，之后会调用**插入式注解处理器处理**，上面说了插入式注解处理器会修改语法树，生成一些额外的代码，经过处理器的处理语法树会有变动，有变动之后，会再次到生成抽象语法树的处理环节，将变动后的代码再次生成抽象语法树，接着再通过注解处理器，如果这次语法树没有被修改，那么就会**生成响应的字节码**，变成 **class 文件**，以上就是整个注解处理器在整个 `javac` 编译源代码生成 class 文件中起到的作用。

了解Lombok之后，尝试使用：

首先需要安装 Lombok 插件，**点击 File->Settings->Plugins，搜索 `Lombok`**，然后点击安装 Lombok 插件：

![image-20200413200914192](D:\Program Files\hexo\blog\source\_posts\Lombok简介\7.png)

在项目pom文件中添加依赖：

```
<dependencies>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <version>1.18.12</version>
        <scope>provided</scope>
    </dependency>
</dependencies>
```

其中 `provided` 表示 jar 包是运行在编译时的，当程序编译成 class 源代码后，这个 jar 包就不会在源代码层面有所体现。

之后就可以在Java对象前加入自己所需的注解，完成开发。

Lombok也有些不如人意的地方，让我们来分析下 Lombok 的优缺点吧！

Lombok 的优点有以下几点：

- 通过注解自动生成样板代码，提高开发效率
- 代码简洁，只关注相关属性
- 新增属性后，无需刻意修改相关方法

但是 Lombok 也有一些缺点：

- 降低了源代码的可读性和完整性，因为代码是自动生成的，所以很多代码是缺失的。
- 加大对问题排查的难度，可能问题定位到不存在的行，无从下手。
- 需要 IDE 的相关插件的支持，需要项目组所有的开发人员安装Lombok的插件，不然项目会提示找不到方法等错误。
- 影响升级，Lombok对代码有很强的侵入性，对JDK的升级有影响。JDK的更新频率每半年就推出一个新的版本，Lombok作为第三方工具，由开源团队维护，它的迭代速度无法保证。