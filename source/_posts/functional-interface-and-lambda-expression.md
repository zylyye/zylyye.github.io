---
title: 函数式接口与lambda表达式
categories: [Java基础]
tags: [Java, lambda]
date: 2018-05-15 21:43:03
---

## 函数式接口

---


函数式接口的定义很简单：**即只包含一个抽象方法的接口**.

<!-- more -->

例如一个简单的函数式接口：

```java
@FunctionalInterface
interface Sumable{
    int sum(int a, int b);
}
```

函数式接口中可以包含多个方法，但**只能有一个抽象方法**，因此，其它方法必须给出默认实现，如：

```java
@FunctionalInterface
interface Sumable{
    int sum(int a, int b);
    default int thenAdd(int a, int b){
        return a + b;
    }
}
```

通常在函数式接口上都会添加一个`@FunctionalInterface`注解，这种注解称为**标记注解**，用来标记这个接口为函数式接口，这个注解不是必须的，但当自定义函数式接口时，建议添加.

## lambda表达式

---

lambda表达式更像是一个简写的匿名内部类，例如现在有这样一个方法：

```java
class ToolKit {
    public static int getSum(int a, int b, Sumable s){
        return s.sum(a,b);
    }
}
```

其中`getSum()`方法依赖`Sumable`接口中的`sum()`方法来完成对参数`a`、`b`的运算，我们可以传递一个匿名内部类来应用一个简单的运算：

```java
ToolKit.getSum(1, 3, new Sumable() {
    @Override
    public int sum (int a, int b) {
        return a + b;
    }
});
```

而这个过程可以使用lambda表达式来简化：

```java
ToolKit.getSum(1,  3, (int a, int b) -> {
    return a + b;
});
```

实际上，在lambda表达式中接收参数的类型可以自动的被编译器推导出，因此参数的类型可以省略：

```java
ToolKit.getSum(1, 3, (a, b) -> {
    return a + b;
});
```

对于如此简单的计算，还可以进一步简化：

```java
ToolKit.getSum(1, 3, (a, b) -> a + b);
```

### lambda表达式的语法

```java
() -> {}
```

lambda表达式的写法看起来像是一个函数，括号`()`中定义要接收的参数列表，符号`->`后紧跟着要执行的代码块.

- **如果符号`->`后面的代码块中只有一个表达式，那么这个lambda表达式就会返回这个表达式的值**，正如最终简化的`ToolKit.getSum()`方法调用示例里的那样：

    ```java
    ToolKit.getSum(1, 3, (a, b) -> a + b);
    ```

- **如果`->`中的执行语句较多，则应该使用`{}`来包围形成代码块**

- **一般情况下，编译器可以根据对应的接口得出参数的类型，因此，参数类型可以省略不写**

- **即使是空参数列表，括号()也不能省去，但如果只有一个参数，则可以省去括号**，例如：

    ```java
    arg -> arg + 1
    ```

- **除了可以像参数一样直接传递给其他方法，lambda表达式还可以用来直接构造函数式接口的子类对象**，例如：

  ```java
  Sumable sumable = (a, b) -> a + b;
  ```


### lambda的方法引用与构造器引用

#### 方法引用

使用lambda表达式可以使用符号`::`直接传递其他类的方法，有三种传递类型，例如对于这样一个类：

```
class LamFunc{
    public void func(int arg){
        // do something
    }
    public static void staticFunc(int arg){
        // do something
    }
}
```

1. **对象::方法名**

    ```java
    new LamFunc()::func
    // 相当于
    x->new LamFunc().func(x)
    ```

2. **类名::静态方法名**

    ```java
    LamFunc::staticFunc
    // 相当于
    x->LamFunc.staticFunc(x)
    ```

3. **类名::静态方法名**

    ```java
    LamFunc::func;
    // 相当于
    (lamFunc,x)->lamFunc.func(x)
    ```

#### 构造器引用

与方法引用类似，构造器引用写法为：**类名::new**

该lambda表达式将会得到一个该类的对象.

如果方法或构造方法重载，那传入该方法时，编译器将会自动根据参数列表自动解析适配.

数组也是类，因此也可以传递数组的构造方法,例如：

```java
int[]::new
String[]::new
```

### lambda表达式的作用域

lambda表达式与一个匿名内部类很相似，其作用域规则与内部类的规则一致，唯一不同的是，**lambda表达式只是一个方法体，其内部的`this`关键字指向外部类对象的引用.**