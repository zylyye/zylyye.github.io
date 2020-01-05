---
title: Java泛型
categories: [Java基础]
tags: [Java, Java泛型]
date: 2018-07-20 21:29:26
---

Java泛型自Java 1.5 引入，可以通过为类或方法添加类型参数定义泛型类或方法，使用泛型可以提高类或方法的通用性，还可以借助编译器的类型检查来尽可能的避免类型的不安全转换.

<!-- more -->

## 泛型类

---

定义一个类时，在类名后添加一个使用尖括号(<>)括起来的类型参数列表，这样的类即为泛型类：

```java
class GenericClassDemo<T> {
    private T var;
    public T getVar() {
        return var;
    }
    public void setVar(T t) {
        var = t;
    }
}
```

这样，在这个类中，就可以使用这些在定义在类型参数列表中的类型变量.

在实例化一个泛型类时，可以根据需要传入某个具体类型：

```java
GenericClassDemo<String> gcd = new GenericClassDemo<>();
```

注意：

- 定义泛型类时，尖括号(<>)中的类型参数可以有多个，多个之间使用逗号隔开
- 泛型参数不能是基本类型(如int, double等)，相应的可以使用基本类型的包装类型取代
- 类型变量可以用于类中的域、方法参数、方法返回类型

**Java中定义类型变量一般使用简短大写的字母，通常情况下，使用`E`表示集合元素的类型，`K`和`V`分别表示键(key)和值(value)的类型,`T`表示任意类型.**

## 泛型方法

---

与泛型类类似，为一个方法添加类型参数列表即可定义一个泛型方法：

```java
class Demo {
    public static <T> T getT() {
        return null;
    }
    public <U> U getU() {
        return null;
    }
}
```

泛型方法可以定义在一个普通类中，它与泛型类没有直接关联，调用一个泛型方法时，需要在方法名前提供类型参数：

```java
String str = Demo.<String>getT();
int i = new Demo().<Integer>getU();
```

注意：

- 泛型方法的泛型参数列表定义在方法修饰符之后(如`public static`)，方法返回类型之前
- 泛型方法的泛型参数可以在方法的参数列表、方法返回类型、方法体中使用
- 调用泛型方法时，有时不需要提供类型参数，因为编译器可以根据语义自动推断出所需类型

## 泛型的类型变量限定

---

在定义泛型类或泛型方法时，可以为类型变量添加限定，以此来限定接收的类型，例如定义一个限定类型为`Comparable`或其子类的泛型：

```java
class Demo<T extends Comparable> {
    ...
}
```

这样在实例化这个类时，指定的类型参数只能为`Comparable`或其子类类型.

**由于Java中的类只能继承一个类，但可以实现多个方法，因此如果在限定的条件是某个类和者多个接口，可以使用(&)符号隔开，如果有类作为限定，则该类必须作为限定列表中的第一个**, 例如：

```java
class Demo<T extends ClassName & Interface1 & Interface2...> {
    ...
}
```

## 通配符类型

---

如果一个方法接收一个具体泛型类的实例，例如：

```java
public void handle(ArrayList<String> list){
  ...
}
```

那么这个方法只能接收`ArrayList<String>`对象，而不能接收指定其它泛型类型例如：`ArrayList<Person>`，当然，可以定义泛型方法来处理这种情况：

```java
public <T> void handle(ArrayList<T> list) {
    ...
}
```

除此之外，还可以使用通配符类型来处理这种情况，Java中可以使用符号`?`来表示任意一种类型，例如如下方法：

```java
public void handle(ArrayList<?> list) {
    ...
}
```

则可以接收任意`ArrayList`的泛型对象.

### 通配符上下限

通配符上限使用关键字`extends`指定，例如：

```java
ArrayList<? extends Serializable>
```

`? extends Serializable`表示通配`Serializable`或其子类类型.

通配符下限使用关键字`super`指定，例如：

```java
ArrayList<? super Serializable>
```

`? super Serializable`表示通配`Serializable`或其父类型.`

## 泛型的类型擦除

---

Java虚拟机并不实际支持泛型，Java的泛型特性主要是由编译器支持，代码中定义的泛型类，编译后由虚拟机解析运行时，仍然是一个普通的类.

**编译器在解析编译泛型类时，会在变量访问、方法调用等合适的地方插入类型转换，然后擦除泛型变量类型.**

例如对于一个泛型类：

```java
class Entry<K, V> {
    K key;
    V value;
    public Demo(K k, V v) {
        key = k;
        value = v;
    }
    public K getKey() {
        return key;
    }
    public V getValue() {
        return value;
    }
}
```

编译后，泛型变量会被擦除，只保留了泛型类的原始类型：

```java
class Entry {
    Object key;
    Object value;

    public Demo(Object k, Object v) {
        key = k;
        value = v;
    }
    public Object getKey() {
        return key;
    }

    public Object getValue() {
        return value;
    }
}
```

如果使用泛型类型限定，则会替换为限定的类，以尽量减少不必要的类型转换，如泛型类：

```java
class Entry<K extends Integer, V extends String> {
     ....
}
```

擦除后：

```java
class Entry {
    Integer key;
    String value;
    ...
}
```

如果限定列表中包含多个限定，则选中第一个类型.

## 类型擦除带来的问题和约束

---

- **泛型类型不能使用基本类型**  类型擦除后，所有的泛型类型都变为`Object`，而基本类型不属于`Object`的子类，因此只能使用基本类型的包装类型来作为泛型类型(如`Integer`, `Double`等)

- **不能使用`instanceof`来校验具体的泛型类**

  例如：

  ```java
  obj instanceof ArrayList<String>
  ```

  类型擦除后，不能保留具体的泛型类型

- **不安全的泛型类数组**

  例如创建一个泛型类数组：

  ```java
  Entry<String, String>[] entrys = new Entry<>[10];
  ```

  `Entry<String, String>`类也是`Object`的子类，因此可以进行以下转换：

  ```java
  Object[] objs = entrys;
  ```

  这样会导致如下的非法的赋值操作通过了编译器检查：

  ```java
  objs[0] = 1;
  ```

  这样的语句在运行时会发生`ArrayStoreException`异常，而编译时却不会发现这样的问题.

  可以使用集合类来避免这个问题，例如`List<Entry<String, String>>`，泛型类型参数不存在多态的特性，因此可以避免上述的问题，如以下语句不会通过编译器检查：

  ```java
  List<Object> = new ArrayList<Entry<String, String>>;
  ```

- **不能直接使用泛型变量创建实例或访问其`Class`对象**

  不能使用诸如`new T()`, `T.class`的语句，因为类型擦除后，`T`类型会被擦除为`Object`

泛型带来的问题根源于编译器的类型擦除以及Java中类的多态性，致使部分不合理的操作会通过编译器的检查成功编译，最后只会在运行期间发现错误.

## 利用反射获取泛型类的信息

---

虽然进行了类型擦除，依然可以通过反射来获取一点有关擦除前泛型类的信息，例如可以通过泛型类`Class`对象的`getTypeParameters()`来获取泛型类定义的泛型参数.

`getTypeParameters()`方法返回了一个`TypeVariable<Class<T>>[]`数组，其中包含了定义的泛型参数列表的信息，例如对于集合类中的`ArrayList`类，可以通过该方法了解到该泛型类定义了一个泛型参数`E`.

### 获取泛型类的具体类型

但是，通过上述方法无法获取具体的泛型类型，例如对一个`ArrayList<String>`类型的对象，无法通过反射获取到泛型变量的值为`String`，不过，子类会保留父类的泛型信息，例如：

```java
class CustomList extends ArrayList<String> {
    ...
}
```

此时，可以通过`CustomList`的`Class`对象获取到其超类的泛型信息：

```java
CustomList.class.getGenericSuperclass();
```

获取具体的泛型类型：

```java
Type[] types = ((ParameterizedTypeImpl) CustomList.class.getGenericSuperclass()).getActualTypeArguments();
Type stringType = types[0];
```

这样即可以获取到实际的泛型类型是`String`.

因此，如需保留泛型类的具体信息，可以创建一个其匿名子类，例如：

```java
List<String> list = new ArrayList<String>() {};
```

这个`list`的`Class`对象中就保留了泛型的具体信息.

## 类型擦除引发的序列化、反序列化问题

---

为了方便传输或保存，经常需要将对象进行序列化，例如接口将数据序列化为json，传递给前台使用. 在对泛型序列化时，一般不会存在什么问题，因为泛型类中的泛型实例有`Class`对象用于保存类型信息，但是如果对此进行反序列化，则会因为类型擦除导致不知道反序列化为哪个类型，例如(以google的Gson为例)：

```java
List<UserInfo> userList = ...;
// 序列化，仍然会保留数据的结构
String json = gson.toJson(userList);
// 由于类型擦除，反序列化之后，集合中存储的是Map类型的对象，而不是UserInfo类型
List<UserInfo> users = gson.fromJson(json);
// 运行期间抛出ClassCastException
UserInfo user = users.get(0);
```

为此，Gson使用了`TypeToken`来解决这个问题，其本质依然是因为泛型类的子类会记录父类的泛型具体信息：

```java
// 创建匿名内部类用于保存泛型信息
Type userInfoType = new TypeToken<List<UserInfo>>() {}.getType();
List<UserInfo> users = gson.fromJson(json);
UserInfo user = users.get(0);
```

