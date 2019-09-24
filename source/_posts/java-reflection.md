---
title: Java反射
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

1. `**ClassName.class`** 知道一个类的类名，就可以使用`.class`来获取该类的Class对象，如`String.class`

2. **通过对象方法`getClass()`** 借助超类中的`getClass()`方法，可以获取对象所属类的Class对象，如`“”.getClass()`

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

Java虚拟机只会为一个类构建一个Class对象，因此，无论通过何种方式获取的某一个类的Class对象，它们都指向同一个对象(即使使用`Class.forName()`方法加载多次，得到的依然是同一个Class对象)，即：

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
public Constructor<?>[] getConstructors();
public Constructor<?>[] getDeclaredConstructors();

// 提供参数类型，获得制定的类构造方法
public Constructor<T> getConstructor(Class<?>... parameterTypes);
public Constructor<T> getDeclaredConstructor(Class<?>... parameterTypes);
```

**其中拥有Declard的方法与没有的区别是，Declared包含该类的全部方法，没有Declared只会给出可见性为public的方法**.

这些方法返回的都是Constructor类的对象或对象数组，该类包含了获得构造方法名及参数等方法.

### 获取方法

Class类中有四个获得有关类中方法信息的方法：

```java
// 获得含有类中方法的数组
public Method[] getMethods();
public Method[] getDeclaredMethods();

// 提供方法名与方法参数，获得指定的方法
public Method getMethod(String name, Class<?>... parameterTypes);
public Method getDeclaredMethod(String name, Class<?>... parameterTypes);
```

**同样，含有Declared的方法会给出所有方法，但不会包含从父类继承过来的方法.**

Method类对象包含了获得方法名与参数等方法.

### 获取数据域

Class类中的四个有关获得类中数据域信息的方法：

```java
// 获得含有数据域的数组
public Field[] getFields();
public Field[] getDeclaredFields();

// 提供变量名，获得指定数据域
public Field getField(String name);
public Field getDeclaredField(String name);
```

其中`Field`对象中包含了获取变量名，类型及存储值等方法.

### 获取成员修饰符及参数等信息

`Constructor`类与`Method`类都实现了`GenericDeclaration`与`Member`接口，`GenericDeclaration`接口定义了获得参数信息的方法，`Member`接口定义了获得成员名与修饰符名的方法：

```java
// GenericDeclaration接口中定义的相关方法
TypeVariable<?>[] getTypeParameters();

// Member接口中定义的相关方法
int getModifiers();
String getName();
```

#### 获取修饰符

成员的修饰符即类似于`public static`这样的关键字序列，Member接口中的`getModifiers()`方法返回用于描述修饰符的整型，可以使用`Modifier`类的静态方法将其转换为字符串描述：

```java
public String toString(int modifier);
```

#### 获取参数信息

`GenericDeclaration`接口中的`getTypeParameters()`方法返回包含参数信息的`TypeVariable`数组，可以使用类中的`getName()`方法获得参数类型名，此外，在`Constructor`和`Method`类中还有`getParameterCount()`方法用于获得参数个数：

```java
public int getParameterCount();
```

#### 获取返回类型

`Method`类中提供了获取方法返回类型的方法：

```java
public Class<?> getReturnType();
```

#### 获取数据域的值

Field类中提供了get方法，将相应的对象传递给这个方法，就可以获得对应域存储的值，该方法是一个通用方法，任何类型的值将被作为Object对象返回，例如对于这样的类：

```java
class Demo {
    public String name;
    private int age;
    public Demo(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

可以利用反射来获取Demo对象中`name`属性的值：

```java
Demo demo = new Demo("name", 123);
// 获取该属性的Fileld对象
Field field = Demo.class.getField("name");
// 将一个Demo对象传递给field的get()方法，获得该域存储的值
String name = (String)field.get(demo);
```

如果数据域存储的是原始类型，则调用该方法时，会自动将其包装成相应的包装类型，同时，Field中还定义了获取原始类型值的方法：

```java
public int getInt(Object obj);
public long getLong(Object obj);
public double getDouble(Object obj);
...
```

如果使用该方法访问私有的成员变量，则方法会抛出`IllegalAccessException`异常，但我们依然可以通过设置该域的可访问性来获取其值，Field类中提供了修改域可访问性的方法：

```java
public void setAccessible(boolean flag);
```

例如访问Demo对象中的age值：

```java
Field field = Demo.class.getDeclaredField("age");
field.setAccessible(true);
int age = field.getInt(demo);
```

## 利用反射动态构造对象与调用方法

### 动态构造对象

Constructor类中定义了用于构造对象的方法：

```java
public T newInstance(Object... initargs);
```

Constructor是一个泛型类，但我们将会只是简单的将构造好的对象转换为Object对象返回，例如，可以编写一个工具方法，接受一个Class对象，利用该对象获得相应类的空参数构造方法，然后利用空构造方法构造这个类的对象并返回：

```java
class Tools {
    public static Object getInstance(Class cl) {
        Object obj = null;
        try {
            Constructor cons = cl.getConstructor();
            obj = cons.newInstance();
        } catch (Exception e) {
            e.printStackTrace();
        }
        return obj;
    }
}
```

### 利用反射调用对象方法

类似于Field对象中的`get()`方法，Method对象中有一个`invoke()`方法：

```java
public Object invoke(Object obj, Object... args);
```

- 该方法的第一个参数是该Method所属的类的一个实例，后面的参数为方法接收的参数
- 如果这是一个静态方法，那么第一个参数可以为`null`
- 如果该方法不接收参数，则可以只有第一个参数
- 该方法会返回原方法调用后返回的值(包装为Object对象),如果原方法无返回值，`invoke()`方法返回null

## 利用反射构建动态构建数组

---

在Java中，数组也是一个类，例如`int[]`,`String[]`等都有各自的Class对象，Class对象中提供了可以获得数组中元素类型的方法：

```java
public Class<?> getComponentType();
```

利用该方法，对于任意一个给定的数组，我们都可以利用反射来获取其中存储的成员类型.

在`java.lang.reflect`包中有一个Array类，该类提供了创建指定类型数组的方法：

```java
public static Object newInstance(Class<?> componentType, int length);
```

该方法的第一个参数表示待创建数组的类型，第二个参数表示带创建数组的长度.

利用该方法创建的数组都被包装为`Object`对象，我们需要手动进行类型转换,例如：

```java
int[] intArr = new int[10];
// 等价于
intArr = (int[]) Array.newInstance(int.class, 10);
String[] strArr = new String[10];
// 等价于
strArr = (String[]) Array.newInstance(String.class, 10);
```

### 创建多维数组

该方法还提供了一个重载的方法，可以用来创建多维数组：

```java
public static Object newInstance(Class<?> componentType, int... dimensions);
```

该方法的第一个参数为多维数组中元素的类型，后面的参数依次为各个维度数组的长度，例如：

```java
int[][] arr = new int[2][3];
// 等价于
arr = (int[][]) Array.newInstance(int.class, 2, 3);
```

需要注意的时，**通过该方法创建的数组维度不能超过255，否则会抛出`IllegalArgumentException`异常.**

