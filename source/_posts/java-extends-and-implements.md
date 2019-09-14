---
title: Java继承类与实现接口
categories: [Java基础]
tags: [Java]
date: 2018-05-07 15:01:30
---

## 类的继承

---

Java中可以通过继承来扩展一个类的功能，复用现有的方法，Java中使用`extends`关键字来进行继承，被继承的类称为父类，继承了其它类的类称为子类.

<!-- more -->

###　定义子类

定义一个普通的类：

```java
class Parent {
    private String name;
    private int age;
    public Parent() {}
    public Parent(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

现在使用`extends`来继承这个类：

```java
class Child extends Parent {
    // add some methods here
}
```

### 继承的规则

- Java只能单继承，即只能同时继承一个类(但可以实现多个接口)
- Java的继承为公有继承，子类中只能访问非私有成员(protected, public, 同一个包中的default)
- 子类中可以使用`super`关键字来访问父类的非私有成员及构造方法
- 子类不能直接访问或覆盖父类的私有成员，但可以通过父类提供的非私有方法间接访问
- 子类覆盖父类成员时，可以放大访问权限(如protect -> public)

### 子类构造方法

子类可以显示的使用`super`关键字来调用父类的构造方法，例如：

```java
class Child extends Parent{
    private int grade;
    public Childs(String name,int age,int grade) {
        super(name,age);
        this.grade = grade;
    }
    // add some new methods
}
```

子类的构造方法中，必须在第一行调用父类的构造方法，如果没有手动调用，则编译器会自动在构造方法中首行添加调用父构造方法的语句`super()`.

**`super`关键子与`this`关键字类似，但有所区别，`this`关键字可以当做一个引用当前对象的变量使用，但`super`不行，例如在一个方法中，这样的语句是合法的：**

```java
Child self = this;
```

### 覆盖父类的方法

有时候父类定义的方法不满足子类的需求，子类希望重写父类的方法，这时，子类可以定义一个与父类相同的方法来覆盖父类中的方法.

重写父类方法时，有如下限制:

- 方法名必须相同
- 方法参数列表必须相同
- 方法返回类型需与原来的返回类型相兼容，即保持相同或者返回的类型为其子类
- 方法的返回权限只能保持一致或着更高，如原来是`private`,可以重写为`public`

例如：

```java
class Parent {
    private String name;
    private int age;
    public Parent(String name, int age) {
        this.name = name;
        this.age = age;
    }
    public String getInfo() {
        return "name:"+name+"\nage:"+age+"\n";
    }
}

class Child extends Parent {
    private int grade;
    public Childs(String name,int age,int grade) {
        super(name,age);
    }
    
    @Override
    public String getInfo() {
        return "grade:"+this.grade+"\n";
    }
}
```

`@Ovrride`注解表示该方法是为了覆盖了父类的方法，非必须.

在子类中可以使用`super`关键字来访问父类的方法：

```java
class Child extends Parent {
    private int grade;
    public Childs(String name,int age,int grade) {
        super(name,age);
        this.grade = grade;
    }
    public String getInfo() {
        // 访问父类的getInfo()方法
        return super.getInfo()+"grade:"+this.grade+"\n";
    }
}
```

如果定义的方法只是与父类方法同名，但参数不同，则该方法会重载.

### 使用final防止继承与重写

`final`关键字可以用在类上，也可以用在类中的变量或方法中.

如果用在类上，则这个类不能被继承，例如：

```java
public final String {
    ...
}
```

Java的预定义类`String`就是一个final类，该类不能被其他类继承.

如果`final`用在类中的变量或方法中，则该变量或方法不能被子类覆盖，例如：

```java
public class FinalClass {
    public final int count;
    
    public final int getCount() {
		return this.count;
    }
}
```

这样的类中，`count`与`getCount`方法不能被子类覆盖.

**有关final的编译器优化**

虚拟机在调用一个对象的方法时，会提取该对象所属类的方法列表，来寻找合适的方法进行调用，如果该对象中没有找到，则从该类的父类中查找，这个搜索方法的过程会有一定的损耗，而如果将方法定义为private、static、final，则虚拟机无需进行这种方法检索而直接调用(这个过程由编译器进行优化，编译器会对这些方法进行**静态绑定**，而其它的无法在编译期间确定的编译器使用**动态绑定**的方式来生成调用指令).

此外，对于没有被继承且简短的方法，编译器会对其进行**内联处理**，从而提升方法的执行效率.

## 抽象类

---

有时在设计一个类时，并不想给出方法的具体实现，或者希望把相似类的共同部分封装成一个类，共同方法的具体实现分别在各自的子类中再给出具体是实现，此时，可以定义一个抽象类来完成这个需求.

### 抽象类与抽象方法

如果只是声明了一个方法，给出了方法名，参数列表，却没有给出具体实现，而是希望子类能够自定义这个方法，这样的方法叫做抽象方法，抽象方法需要使用`abstract`关键字修饰，如果一个类中包含抽象方法，则这个类就是抽象类，也必须使用`abstract`关键字来修饰.例如：

```java
abstract class Connection {
    public String url;
    public Connection(String url) {
        this.url = url;
    }
    public abstract boolean connect();
    public abstract void close();
}
```

### 抽象类的定义规则

- 抽象类与普通类类似，在抽象类中可以定义抽象方法，抽象方法不能私有
- 抽象类不能实例化，因为可能存在没有具体实现的方法
- 抽象类中可以没有抽象方法
- 普通类继承了一个抽象类时，必须给出抽象类中所有抽象方法的具体实现
- 抽象类之间也可以相互继承，且可以按需实现部分抽象方法

## 接口

---

相比于抽象类，接口是更加抽象的特征集合，接口中只描述了某些类的公共特征，并不给出具体实现.

### 定义接口

定义一个类时使用`class`关键字，定义接口时使用`interface`关键字，与从抽象类不同的是，接口中定义的方法全部为抽象方法，此外还有以下规则：

- 接口中定义的变量都必须是公共静态的常量，定义的方法都必须是公共抽象方法，在接口中定义变量或方法时，可以不用添加修饰符，编译器会自动为变量添加`public static final`，为抽象方法自动添加`public abstract`
- 与类只能单继承不同，一个类可以实现多个接口，多个接口之间使用`,`分隔
- 接口之间可以使用`extends`继承
- 普通类实现一个接口时，必须实现其全部方法，抽象类可以按需只实现其中一部分
- 接口中可以使用`default`为抽象方法定义一个默认实现，子类在实现该接口时，拥有默认实现的方法，可以不用重写，保留默认实现

例如：

```java
interface Connection{
    int MAX_CONNECTED_COUNT = 10;
    void connect();
    void init();
    default void close(){
        System.exit(0);
    }
}
```

### 接口默认方法冲突

由于接口中可以定义默认方法，且接口支持多实现，因此，可能会发生以下几种冲突问题：

1. **子类实现的多个接口中，多个接口中存在同名方法，且都有默认实现**

   此时子类中必须重写该方法以避免方法冲突问题

2. 子类实现的接口中与继承的类中有同名的方法，且接口中提供了默认实现

   **此时父类中的方法优先，接口中的默认方法将被忽略**



## 类的公共超类`Object	

---

Java中定义的类会默认继承与一个预定义类`Object`，该类为所有类的公共超类，`Object`中定义了几个有用的方法.

### `Object`类中的常见方法

```java
public final native Class<?> getClass();
public native int hashCode();
public boolean equals(Object obj) {
        return (this == obj);
}
protected native Object clone() throws CloneNotSupportedException;
public String toString() {
        return getClass().getName() + "@" + Integer.toHexString(hashCode());
}
```

除了这些方法之外，`Object`中还提供了与多线程相关的方法.

### 对象克隆

`Object`类中有一个用于对象克隆的方法`clone()`，该方法访问权限为`projected`，即无法在其他类中直接访问该方法来进行克隆方法，这么做的原因是默认实现的`clone()`方法是不安全的，它只会对这个对象进行**浅拷贝**，即如果对象中保存了其它对象的引用，那么克隆的对象只是简单拷贝了这个引用而非引用所指的对象.

为此，如果需要让一个对象提供克隆的功能，我们需要重新自定义这个`clone()`方法，并将该方法定义为`public`以供外部使用，Java为此定义了一个`Cloneable`接口，为了规范，一个类需要实现该接口并重写`clone()`方法来标识该类的对象对象克隆，例如：

```java
class Student implements Cloneable{
    private String name;
    private int age;
    private LocalDate start;
    public Student(String name,int age){
        this.name = name;
        this.age = age;
        start = LocalDate.now();
    }
    @Override
    public Student clone() throws CloneNotSupportedException {
        Student student = (Student)super.clone();
        student.start = LocalDate.now();
        return student;
    }
}
```

## 多态

---

**多态**可以理解为多种形态，例如某个接口具有多种实现，某个类具有多个子类，这些实现或子类就是接口或其父类的多种形态，在Java 中多态提供了一个很有用的特性：父类类型的变量可以存储子类对象的引用.

例如，对于一个子类：

```java
class Student extends People {
    ...
}
```

则可以使用`People`类型的变量来存储`Student`类型的对象：

```java
People student = new Student();
```

同理，在定义方法的参数列表时，也可以充分利用多态带来的灵活性，例如：

```java
class Tools {
    public static void showInfo(People people) {
        System.out.println("name:"+people.getName()+"\nage"+people.getAge());
    }
}
```

调用`showInfo()`方法时，可以传入`People`的子类：

```java
Tools.showInfo(New Student());
```

如果`Student`类中重写了父类的方法，虽然`Student`对象的引用存储在其父类类型的变量中，但实际调用时，还是调用的`Student`类中的方法，因为在调用方法时，调用的主体会作为隐式参数传递给该方法(**this**).

多态性质很灵活实用，但依然存在限制，例如，当需要调用子类特有的方法时，需要将类型强制转换为子类类型，才可以调用子类中特有的方法，例如对于这样的父类与子类：

```java
class Father {
    public void showInfo() {
        System.out.println("father");
    }
}

class Child extends Father {
    public void showInfo() {
        System.out.println("Child");
    }
    public void extraFunc() {
        System.out.println("extraFunc");
    }
}
```

利用多态性质可以这样定义和存储对象：

```java
Father father = new Child();
```

但这样只能调用`Father`类中的方法：

```java
father.showInfo();	// 输出 Child
father.extraFunc();  // 编译错误，找不到该方法
```

这是因为编译器会根据当前类型来生成其可以调用的方法列表，此时`Child`对象存储在`Father`类型变量中，则只能查找`Father`类的方法列表，要想调用`Child`类中的方法，必须将其强制转换为`Child`类型：

```java
Child child = (Child)father;
child.extraFunc();
// 或者
((Child)father).extraFunc();
```

