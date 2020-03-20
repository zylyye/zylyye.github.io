---
title: JAR包
categories: [Java基础]
tags: [Java, JAR]
date: 2020-03-15 12:02:44
---

JAR 包是一个 ZIP 格式的压缩包, 通常使用 JAR 包来打包 Java 程序及其使用的相关资源文件, 以便于程序的发布、传输和运行。

<!--more-->

## JAR 包的特点

---

- 便于传输
- 压缩文件便于存储， 节省空间
- 提升安全性（可进行签名与验证）
- 可进行包密封（Package Sealing）
- 可用于扩展系统核心库
- 可携带额外的描述信息（发布者、版本等信息）
- 不依赖平台（portability）

## 使用 JAR 包

---

### 创建 JAR 包

使用 JDK 提供的打包命令行工具 jar 创建一个 JAR 包：

```bash
jar cf file-name.jar files-or-directories
```

例如，将当前文件夹下的 class 文件和 images 目录下的文件打包进 JAR 包中：

```bash
jar cf app.jar *.class images
```

该命令的其它可选选项：

| 选项     | 描述                                                         |
| -------- | ------------------------------------------------------------ |
| v        | 打包时显示详细信息，会显示所有打包文件的文件名               |
| 0(数字0) | 指定压缩等级，0 表示不进行压缩                               |
| M        | 不自动生成 JAR 包的清单文件（manifest）                      |
| m        | 手动指定清单文件（manifest），打包时将会把指定清单文件中的内容放入 JAR 包 |
| -C       | 临时更改 jar 命令执行时的当前文件夹                          |

对于 `-C` 命令，例如现在需要打包 images 目录下的所有图片，而不是连同 images 目录打包，可以使用 -C 来临时将当前目录更改为 images 目录进行打包：

```bash
jar cf app.jar -C images .
```

### 查看 JAR 包

查看一个 JAR 包内的文件列表：

```bash
jar tf app.jar
```

可以使用 v 选项来展示更详细的信息（文件字节大小、创建时间等）：

```bash
jar tvf app.jar
```

### 解压 JAR 包

解压 JAR 包内的全部文件：

```bash
jar xf app.jar
```

解压 JAR 包内指定文件：

```bash
jar xf app.jar filenames
```

### 更新 JAR 包

更新 JAR 包的命令可以用来新增或覆盖 JAR 包中的文件：

```bash
jar uf app.jar add-files
```

如果 JAR 包中已包含指定的文件，则直接覆盖，否则新增

### 运行 JAR 包

一般的 JAR 包仅仅只是打包了需要的文件，如果希望 JAR 包可以直接运行，需要在 JAR 包的清单文件(manifest)中指定运行的主类(Main-CLass), 运行的方式如下：

```bash
java -jar app.jar
```

**还可以使用 javaw 来运行 JAR 包，区别是 Javaw 不会打开命令行窗口。**

还可以在运行 JAR 包时指定虚拟机参数，例如指定最大允许分配内存为 512 m：

```bash
java -jar -Xmx512m app.jar
```

## MANIFEST 文件

---

MANIFEST 是 JAR 包的清单文件，用来存储 JAR 包和包中文件的属性信息，MANIFEST文件在 JAR 包中的存储路径为 META-INF/MANIFEST.MF。

### MANIFEST 文件的定义格式

**属性命名方式：**

```java
Attr-Name: value
```

属性名称中每个单词首字母大写，单词间使用中划线隔开，属性名称和属性值之间使用逗号隔开，例如：

```
Manifest-Version: 1.0
Main-Class: com.example.Main
```

**清单文件中定义的属性分为两种**：

- 全局属性 - 针对整个 JAR 包及其中的所有文件
- 局部属性 - 针对某个文件夹或文件，局部属性会覆盖全局属性

全局属性定义在清单文件的开始部分，局部属性定义在全局属性之后，例如：

```
Manifest-Version: 1.0
Main-Class: com.example.Main
Sealed: true
Name: some/package/
Sealed: false
```

其中属性 Name 用于指定局部属性，该属性的值指定了作用的文件或文件夹（包）。

### 创建、更新 MANIFEST 文件

MANIFEST 文件会在每次使用 `jar cf` 命令创建 JAR 包时自动生成（除非指定了 M 参数来禁用自动生成），默认生成的 MANEFIST 文件通常会包含一些基础的描述信息：

```
 Manifest-Version: 1.0
 Created-By: 1.8.0_171 (Oracle Corporation)
```

可以指定一个自定义的清单文件，来添加需要的属性：

```bash
jar cfm app.jar manifest-add.mf files
```

其中 manifest-add.mf 是纯文本文件，内容包含了自定义的属性内容，**该操作会将自定义清单文件的内容添加到自动生成的清单文件中。**

## 可运行的 JAR 包

---

如果希望 JAR 包可以执行，需要在 JAR 包中的清单文件中指定应用的入口：

```
Main-Class: com.example.classname
```

Main-Class 来指定应用中包含 main 方法类的全限定名，有两种设置该属性的方式：

- 在自定义清单文件中添加

- 使用 jar 命令打包时，使用选项 e 来指定 Main-Class 的值，例如：

  ```bash
  jar cfe app.jar com.example.classname files
  ```

指定了 Main-Class 后的 JAR 包，可以使用 java 命令运行：

```java
java -jar app.jar
```

## 参考资料

---

- <https://docs.oracle.com/javase/tutorial/deployment/jar/index.html>

- <https://docs.oracle.com/javase/10/docs/specs/jar/jar.html>

- 《Java 核心技术卷》卷一 第 13 章 第十版

  