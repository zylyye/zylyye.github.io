---
title: Java日志API
categories: [Java基础]
tags: [Java]
date: 2018-07-18 21:36:58
---

在编写程序时，经常需要使用输出工具（例如常用的`System.out.println()`）来输出、记录或者对比某些变量的值，在程序的运行期间，需要记录一些关键日志信息来调试、监控或者方便以后排查问题，此时，一般输出语句无法满足日志记录的需求，因此，Java内部提供了一套用于日志记录的API.

<!-- more -->

## 日志API提供的好处

---

使用专门的日志工具来记录日志可以让这项工作更加专业和灵活性，例如：

- 日志信息更加统一、标准
- 可以配置记录日志的级别（如只记录错误信息，忽略调试信息）
- 可以更容易的定位错误日志发生的位置
- 可以更方便的配置日志保存的位置

## 记录简单的日志

---

需要记录一个日志只需要简单的几个步骤：

1. 获取日志记录器

   ```java
   // 获取全局日志记录器
   Logger logger = Logger.getGlobal();
   ```

2. 打印指定级别的日志

   ```java
   logger.info("global message");
   ```

这样，在程序中插入这条日志记录语句，控制台就会输出打印日志的具体类，具体方法以及自定义的日志信息.

## 日志级别

---

日志API中定义了几个日志级别来帮助我们打印不同重要程度的日志：

- SEVERE
- WARNING
- INFO
- CONFIG
- FINE
- FINER
- FINEST

这些级别中，最严重的是SEVERE级别，最普通的是FINEST级别，当程序发生严重错误时，应该记录SEVERE级别的日志，进行调试时或记录一般信息时，可以记录INFO级别或者更低级别(如FINE)的日志.

### 配置日志记录级别

获取日志记录器后，可以使用`Logger`对象提供的`setLevel()`方法设置记录的最大级别，例如：

```java
logger.setLevel(Level.WARNING);
```

这样级别在WARNING以下的日志将不会记录，例如：

```java
logger.info("info log"); // 不会打印
```

## 日志记录器的层次结构

---

获取日志记录器时，一般不使用`Logger.getGlobal()`来获取并使用全局日志记录器，通常情况下，应该为日志记录器提供一个名称，例如：

```java
Logger.getLogger("com.example.tech");
```

全局记录器也是一个普通的记录器，只是其名称为`global`：

```java
Logger.getGlobal() == Logger.getLogger("global");	// true
```

需要注意的是，这个像包名一样的名称还代表这一个层次结构，如名字为`com.example`的日志记录器就是`com.example.tech`的父级记录器，父级和子级之间存在着配置继承关系，例如在子级记录器中没有配置日志记录级别，则会默认使用父级设置的级别.

### 获取父记录器及根日志记录器

可以使用`Logger`对象的`getParent()`来获取当前记录器的父记录器，日志记录器的层次结构类似于类的继承关系，所有类都有一个公共父类`Object`，类似的，所有日志记录器都有个公共父记录器：

```java
Logger.getLogger("");	// 根日志记录器
```

所有的日志记录器都是其子级，包括全局记录器（因为全局记录器只是名字为`global`的普通记录器），根日志记录器有默认配置(如默认的日志记录级别为INFO)，每次获取的日志记录器将默认使用该默认配置.

**根日志记录器的默认配置文件位于JDK目录下的`jre/lib/logging.properties`，可以通过修改该文件中的配置来修改根日志记录器的默认配置.**

## 日志处理器(Handler)

---

可以将日志记录器看成一个日志信息的收集器，记录器只是收集指定级别的日志，这些日志应该输出到什么地方则交由日志处理器来处理，`Logger`对象中定义了`addHandler()`方法用于向一个日志记录器添加处理器. 每个日志记录器可以添加多个处理器.

### 预定义的日志处理器

- ConsoleHandler

  该处理器会将日志输出到控制台中，默认的日志记录器就带有该处理器，因此，无需任何配置，记录日志时，控制台就会打印信息.

- FileHandler

  该处理器用于将日志输出到文件中.

- SocketHandler

  该处理器用于将日志输出到Socket连接中.

- StreamHandler

  该处理器用于将日志输出到指定输出流中，ConsoleHandler、FileHandler、SocketHandler就是继承自该处理器.

- MemoryHandler

  该处理器用于将日志临时缓存在内存中(最大缓存1000条，超出后，旧的日志将被新的覆盖)，在调用`push()`方法时，缓存的日志将被输出到其中配置好的处理器中，此外，该处理器有个主动输出级别阀值(PushLevel，可调用`setPushLevel()`配置该阀值)，当记录的日志级别等于或高于该阀值时，就会主动调用`push()`.

如果有特殊需求，可以自己继承`java.util.logging.Handler`来自定义一个日志处理器.

### 日志处理器的级别

日志记录器只收集指定级别的日志，日志处理器也需要配置只处理指定级别的日志(默认为INFO)，它们之间互相独立，多个日志处理器之间也可以分别设置不同的处理级别. `Handler`对象提供了`setLevel()`方法用于设置处理器的处理级别.

### 日志处理器的过滤器

每个日志处理器还可以使用`setFilter()`设置一个过滤器(`Filter`)，如果需要更加细粒的过滤，可以实现`java.util.logging.Filter`接口来自定义日志的记录条件，该接口中只有一个抽象方法：

```java
public boolean isLoggable(LogRecord record);
```

`LogRecord`对象中包含有日志的详细信息(例如日志级别、内容等等)，可以根据这些信息实现自己的日志过滤需求.

### 日志处理器的格式化器

如果需要自定义日志的格式，可以使用`setFormatter()`为日志处理器设置一个日志格式化器，Java内置了两种日志格式化器：

- SimpleFormatter

  用于将日志格式化为简洁可读的格式，ConsoleHandler默认使用的就是该格式化器.

- XMLFormatter

  将日志格式化为XML文档格式，方便存储分析，FileHandler默认使用的为该格式化器.

实现自定义的格式化器时，只需继承`java.util.logging.Formatter`对象，并实现以下一个方法即可：

```java
public abstract String format(LogRecord record);
```

## 日志管理器

---

Java中有一个全局唯一的日志管理器LogManager，日志管理器中维护了日志记录器和相关功能的状态，其功能主要包括：

- 管理不同日志管理器的层次结构
- 管理日志的配置属性

全局的日志管理器方便了日志记录器的获取、存储和管理，在整个程序中，通过相同名字获取的日志管理器也都指向同一个对象.

### 获取日志管理器

可以通过`LogManager`类提供的静态方法获取这个全局唯一的日志管理器：

```java
public static LogManager getLogManager();
```

### 日志管理器配置属性

日志管理器对象在类初始化时创建，初始化时会默认读取JDK目录下`jre/lib/logging.properties`配置文件来获取默认的配置，可以在运行Java程序时指定属性`java.util.logging.config.file`的值来更改默认配置文件的位置，例如：

```java
java -Djava.util.logging.config. file=...
```

或者，可以调用`LogManager`对象的`readConfiguration(InputStream in)`方法来重新初始化默认配置，自JDK 9 起，这个方法被`updateConfiguration()`取代.

## 日志i18n国际化

---

在获取日志记录器的方法`Logger.getLogger()`方法中，提供了第二个参数：

```java
public static Logger getLogger(String name, String resourceBundleName);
```

`resourceBundleName`指向一个资源文件，其中定义了相应语言的对照文本，例如现在创建了某个语言资源包`i18n/log_zh.properties`和`i18n/log_en.properties`，内容分别为：

```properties
# log_zh.properties文件内容
login=登录

# log_en.properties文件内容
login=sign in
```

现在再来打印一下日志：

```java
Logger logger = Logger.getLogger("", "i18n.log");
logger.info("login");	// 日志内容 login 将被转换为 登录
```

此外，资源包中的内容还可以添加占位符({0}, {1}…)，例如：

```properties
login=欢迎您，{0}
```

现在打印一下带参数的日志：

```java
Logger logger = Logger.getLogger("", "i18n.log");
logger.log(Level.INFO, "login", "zylyye");	// 日志内容被替换为 欢迎您，zylyye
```

