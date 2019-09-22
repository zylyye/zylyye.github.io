---
title: java-reflection
categories: [Java基础]
tags: [Java, Java反射]
date: 2018-05-12 22:01:48
---

---

通过使用Java反射，我们可以在程序运行期间获取任意类的属性及方法、动态的创建它们的对象、动态的访问它们的属性与方法.

<!-- more -->

## Class类

---

Java中有一个专门用于存储某个类的内部信息的对象，这个对象就是Class对象，Class类中定义了许多有关反射的方法，该类没有公共构造方法，只能在运行时被Java虚拟机自动构建，Java虚拟机会为每个被加载的类构建一个相应的Class类的对象.

### 获取类的Class对象

对于一个已经被Java虚拟机加载的类，Java中提供了两种用于获取其Class对象的方法：

1. `ClassName.class` 知道一个类的类名，就可以使用`.class`来获取该类的Class对象，如`String.class`

2. 通过对象方法`getClass()` 借助超类中的`getClass()`方法，可以获取对象所属类的Class对象，如`“”.getClass()`

对于一个未加载的类，可以使用Class类提供的静态方法来手动加载并获取该类的Class对象：

```java
// 手动加载Thread类并获取其Class对象
try {
    Class threadClass = Class.forName("java.lang.Thread");
} catch (ClassNotFoundException e) {
    e.printStackTrace();
}
```

该方法定义如下：

```java
public static Class<?> forName(String className);
```

使用该方法来手动加载类时，需要提供其类名及完整包名，且需要保证该类在当前程序能访问的类路径下，否则，该方法将抛出`ClassNotFoundException`异常.

Java虚拟机只会为一个类构建一个Class对象，因此，无论通过何种方式获取的某一个类的Class对象，都是相同的对象(及时使用`Class.forName()`方法加载多次，得到的依然是同一个Class对象)，即：

```java
Class.forName("java.lang.Thread") == Thread.class; // true
new Thread().getClass() == Thread.class; // true
```

## 利用反射机制访问类的内部信息

---

### 获取构造方法

Class类中有四个有关获得构造方法信息的方法：

```java
// 获得某个含有某个类的构造方法的数组
public Constructor<?>[] getConstructors()；
public Constructor<?>[] getDeclaredConstructors()；

// 提供参数类型，获得制定的类构造方法
public Constructor<T> getConstructor(Class<?>... parameterTypes)；
public Constructor<T> getDeclaredConstructor(Class<?>... parameterTypes)；
```

**其中拥有Declard的方法与没有的区别是，Declared包含该类的全部方法，没有Declared只会给出可见性为public的方法**.

这些方法返回的都是Constructor类的对象或对象数组，该类包含了获得构造方法名及参数等方法.

### 获取方法

Class类中有四个获得有关类中方法信息的方法：

```java
// 获得含有类中方法的数组
public Method[] getMethods()；
public Method[] getDeclaredMethods()；

// 提供方法名与方法参数，获得指定的方法
public Method getMethod(String name, Class<?>... parameterTypes)；
public Method getDeclaredMethod(String name, Class<?>... parameterTypes)；
```

**同样，含有Declared的方法会给出所有方法，但不会包含从父类继承过来的方法.**

Method类对象包含了获得方法名与参数等方法.

### 获取数据域

Class类中的四个有关获得类中数据域信息的方法：

