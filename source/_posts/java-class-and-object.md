---
title: Java类与对象
date: 2018-05-04 15:44:58
categories: [Java基础]
tags: [Java]
---

## 定义简单类

---

Java中使用class关键字来定义一个类，所有的变量及方法必须定义在类中：

```java
class Person {
    private String name;
    private int age;
}
```

<!-- more -->

Java中每个源文件中可以包含多个类，但一个类中只能有一个public类，对编译器来说，一个源文件是一个编译单元，只有public类可以在外部可见，其他非public类只能在本包中可见，及每个编译单元只对外提供一个接口，编译后，每个类生成一个class文件，例如：

```java
// Demo.java 文件中的内容
public class Demo {
    ...
}

class Other {
    ...
}
```

对于像*Demo.java*这样一个包含多个Class的源文件，当使用`javac`命令进行编译时，会产生多个class文件。

如果想要运行这个类，必须在类中定义一个`main`方法:

```java
class Demo {
    public static void main(String[] args){
        ...
    }
}
```

`main`方法是一个静态方法，任何类都可以定义一个main方法，同一个文件中的每个类中也可以同时都定义各自的main方法，但一次只会调用一个类中的main方法(取决于你运行哪个class文件).

通常一个简单可运行 的Java源文件中应给这么定义类：

```java
public class Demo{
    public static void main(String[] args){
        System.out.println("some text here");
    }
}
```

class前的修饰符`public`主要与该类的访问权限有关，不影响类中`main`方法的执行.

## 类的实例化

---

类的实例化即使用某个类来创建该类对象的过程，Java中使用new关键字进行类的实例化,如对于文章开头定义的`Person`类, 创建该类的一个对象：

```java
new Person();
```

使用`new`关键字创建一个类的对象时，会返回这个新创建对象的引用，通常需要使用该对象所属类型的变量来存储这个引用，以备后续使用：

```java
Person person = new Person();
```

## 类的访问控制

---

Java中的类拥有两种访问权限：

- package-private 默认权限(class前没有修饰符的情况)，只有本包中的类可见
- public 所有包中的类可见

对于类中的成员变量与成员方法，有四种访问权限：

- package-private 默认权限(变量或方法前无修饰符的情况)，只有本包中类可访问
- public 所有包中的类可访问
- protected 本包中的类以及外包的子类可以访问
- private 只有本类可访问

**默认权限package-private与protected很相似，两者的区别主要体现在当外包想要访问该成员时，拥有默认权限的成员外包无法访问，而对于protected的方法，只需继承这个方法所在的类，即可访问这个方法.**

## 类的变量与方法

---

一个类主要由变量与方法组成，直接定义在类中的变量与方法称为**成员变量**与**成员方法**.

### 成员变量与局部变量

成员变量：

- 定义在类中
- 在整个类中有效
- 实例化该类时，会自动初始化，数值型初始化为0，布尔型初始化为false，其他对象类型初始化为null；
- 非静态变量随着对象存在和消亡

局部变量：

- 定义在一个方法中
- 只在定义它的方法中有效
- 不会自动初始化
- 只在方法执行时存在

### 成员方法

成员方法即直接定义在类中的方法，通常用来访问或修改对象中的数据，一个方法的定义包含**访问修饰符**(可选)，**返回值类型**(没有则为void)，**方法名**，以及括号里要接收的**参数列表**，如：

```java
class Person {
    private String name;
    public void setName(String name) {
        this.name = name;
    }
}
```

**类中的`setName`方法定义了一个`String`类型的参数`name`，该参数称为显式参数，该方法被调用时，还会传递一个隐式参数，如在方法中的关键字this即表示传递进来的隐式参数，它指代方法所处对象.**

### 方法的可变参数

定义一个方法时，可以声明一个可变的参数列表：

```java
public void show(int... arr){
    if(arr.length > 0){
        for(int i:arr){
            System.out.println(i);
        }
    }
}
```

调用该方法时，传入的参数会自动被包装成指定类型的数组.

### 静态变量与静态方法

要想访问类中的成员变量及成员方法，必须先实例化该类，然后再通过这个对象访问其中的成员，但如果将变量与方法定义为静态的，并且外部可访问，则可以直接通过类名访问，如：

```java
public class Demo{
    public static void main(String[] args){
        Other.count++;
        Other.func();
    }
}
class Other{
    public static int count;
    public static void func(){
        // some statements
    }
}
```

静态变量与方法的特点：

- 无须实例化类即可访问，也可以使用该类的对象访问
- 该类所有的对象共享同一个静态变量或方法
- 静态方法只能访问静态变量或方法，要想访问非静态成员，必须创建相应的对象

除了静态变量，与之类似，也可以定义静态常量.

### 方法重载

在定义方法时，可以定义多个同名的方法（包括静态方法)，这些方法通过接收不同的参数来相互区分，调用时，编译器会根据传入的参数，自动的选择相匹配的方法，这个过程称为**重载解析**.

因此，在定义同名的方法时，只需保证参数列表不同，方法的返回类型可以不同，例如以下的定义时合法的：

```java
public int get(int age) {
    return age;
}
public String get(String name) {
    return name;
}
```



## 构造方法

---

类中的构造方法会在实例化该类时自动调用，可以自定义构造方法，如果没有自定义构造方法，编译器会为类生成一个空参数列表的构造方法.

### 定义构造方法

与方法类似，但构造方法没有返回类型，并且构造方法名必须与类名相同：

```java
class Other {
    int count;
    public Other() {
        var = 0;
    }
}
```

与方法一样，构造方法也可以重载：

```java
class Other {
    private int count;
    public Other() {
        // 可以不用手动将该变量初始化为，成员变量会自动被初始化为0
        count = 0;
    }
    public Other(int count) {
        this.count = count;
    }
}
```

这样在创建该类的对象时，可以将值传入该构造函数：

```java
Other other = new Other(9);
```

### 调用其它构造方法

有时候，为了重用其他构造方法，可以使用**this**手动调用类中的其他构造方法(必须在执行其他语句前先调用其他构造方法)

```java
class Student{
    private int age;
    private String name;
    public Student(String name) {
        // 当局部变量与成员变量重名时，可以使用this来显示的区分成员变量与局部变量
        this.name = name;     
    }
    public Student(String name,int age) {
        // 必须是第一句
        this(name);     
        this.age = age;
    }
}
```

## 初始化块

---

当成员变量的初始化过程比较复杂时，可以将初始化过程放在初始化块中：

```java
class Demo {
    private int count;

    // 初始化块
    {
        count = 0;
    }
}
```

对于静态成员，可以使用静态初始化块：

```java
class Demo {
    private int count;
    private static int staticCount;

    // 静态初始化块
    static {
        staticCount = 0;
    }
}
```

执行顺序：

1. 当类第一次被加载时，静态初始化块执行(静态初始化块只会执行一次)
2. 构造该类的对象时，非静态初始化块执行
3. 构造方法执行

## 内部类

---

在一个类的内部，还可以定义类，这样的类称为内部类，包含该内部类的类称为外部类。

### 成员内部类

成员内部类定义在一个类的内部，与类成员方法处于一个层级.

- 定义成员内部类时，类似于定义类中的成员变量，可以使用public，protected，private修饰符，这些访问权限修饰符会控制成员内部类的可见性
- 成员内部类可以直接访问外部类的成员，例如：

```java
class Outer {
    private int count;
    // 成员内部类
    private class Inner {
        public void init(){
        	// 访问Outer类的成员变量
            count = 10;
        }
    }
    public void setCount() {
        Inner inner = new Inner();
        inner.init();
    }
}
```

当调用`Outer`对象中的`setCount()`方法时，内部类`Inner`对象会被创建，`Inner`对象的`init()`方法可以轻易地更改外部类的私有成员`count`.

- 如果内部类中含有与外部类同名的成员，这样直接访问的方式只会访问内部类自己的成员，此时可以借助**OuterClass.this**来显示访问外部类的成员，如：

```java
class Outer {
    private int count;
    public class Inner {
        public void init(int count) {
            Outer.this.count = count;
        }
    }
    public void setCount() {
        Inner inner = new Inner();
        inner.init(10);
    }
}
```

- 如果成员内部类在外部可见，可以在其它类中创建一个内部类对象：

```java
// 其他类中
Outer.Inner inner = new Outer().new Inner();
```

由于成员内部类不是静态的，因此必须创建一个外部类的对象，再创建内部类对象.

### 静态内部类

可以在类中使用`static`关键字定义一个静态内部类。

- 与成员内部类不同，静态内部类中可以定义静态变量或方法；
- 类似于静态方法，在其它类中创建一个静态内部类的对象，不需要先创建其外部类的对象：

```java
class Outer{
    public static class Inner{
        
    }
}
```
在其它类中，可以直接创建一个Inner对象：

```java
Outer.Inner inner = new Outer.Inner();
```

- 在接口中定义的成员内部类会被转变为静态内部类

### 局部内部类

定义在局部作用域的类即为局部作用域(如定义在一个方法中的类).

- 局部内部类不能使用访问权限修饰符(public,protected,private);
- 局部内部类不仅可以访问外部类的成员，还可以访问局部变量，但是，在内部类中不能改变该变量，否则编译器会报错(相当于要求该局部变量是常量):

```java
class Outer{
    public void set(){
        int count = 10;
        class Inner{
            public void init(){
            	// 编译报错
                count++; 
            }
        }
        new Inner().init();
    }
}
```

但是可以将该变量保存在一个一维数组中，这样指向这个数组的变量不会发生改变，但可以改变数组中的元素：

```java
class Outer{
    public void set(){
        int[] count = {10};
        class Inner{
            public void init(){
                count[0]++;
            }
        }
        new Inner().init();
    }
}
```

### 匿名内部类

匿名内部类用于直接创建一个类的子类对象，其创建语法如下：

```java
new SuperType {
    // Override some methods
}
```

在后面的花括号中，可以覆盖父类的方法.

匿名内部类没有构造函数.

如果在局部域中创建一个匿名内部类，则这是一个匿名局部内部类，其遵守局部内部类的规则，如果在外部类中创建匿名内部类，则这是匿名成员内部类，遵守成员内部类规则.

例如，通常在给图形界面绑定事件时，经常使用匿名内部类：

```java
public class Demo {
    public static void main(String... args){
        JFrame frame = new JFrame();  // 创建一个窗体框架
        frame.setSize(800,600);       // 设置窗体大小
        frame.addWindowListener(new WindowAdapter() {    // 添加事件
            @Override
            public void windowClosing(WindowEvent e) {
                //do something
                System.exit(0);    // 退出程序
            }
        });
        frame.setVisible(true);     // 设置窗体可见
    }
}
```

## 包

---

Java中的包是用来组织和管理类，类似于其他语言中的命名空间与文件系统的文件夹，使用包能更加有条理的管理与归类Java中的类，同时可以避免同名类冲突.

### 定义包

在Java文件中，使用关键字`package`定义当前文件中的类属于哪个包，包定义在除注释外的第一行：

```java
package com.example.tools;

public class Demo...// 文件中定义多个类均属于该包
```

### 导入包中的类

如果需要使用刚才定义在`com.example.tools`包中的`Demo`类，需要在文件顶部使用关键字import进行导入：

```java
import com.example.tools.Demo;
```

不止可以导入类，还可以使用`import static`导入静态成员或方法，如`out`是`System`类中的静态成员，通常使用`out`的成员方法在控制台输出文本:

```java
System.out.println("some text here");
```

可以静态导入out来方便的调用该方法：

```java
import static java.lang.System.out;

public class Demo{
    out.println("some text here");
}
```