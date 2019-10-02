---
title: Java代理对象
categories: [Java基础]
tags: [Java, 代理对象]
date: 2018-06-30 16:49:25
---

Java代理对象为对象的方法调用带来的极大的灵活性，通过使用代理对象，我们可以实现对象的方法跟踪、过滤，甚至动态实现某个具体的接口.

<!-- more -->

## 创建代理对象

---

Java反射包中有一个提供了代理对象相关功能的类`java.lang.reflect.Proxy`，该类提供了创建代理对象的静态方法：

```java
public static Object newProxyInstance(ClassLoader loader, Class<?>[] interfaces, InvocationHandler h);
```

该方法接收三个参数：

- `ClassLoader loader`  提供一个类加载器用于生成代理类的Class对象
- `Class<?>[] interfaces ` 代理类要实现的接口
- `InvocationHandler h`   自定义的方法调用处理器

当需要为某个类创建代理时，需要提供该类实现的接口以及相应的方法调用处理器(类加载器可以使用默认类加载器)，调用代理对象中的方法时，只能调用接口中的方法.

## 自定义方法调用处理器

---

自定义方法调用处理器需实现`InvocationHandler`接口，该接口中只有一个抽象方法：

```java
public Object invoke(Object proxy, Method method, Object[] args) throws Throwable;
```

当调用代理对象中的方法时，会将代理对象、调用方法以及提供的参数传递给该方法，如果需要代理某个具体的对象，则需要将其保存在自定义方法处理器中，以便调用.

## 简单的代理对象示例

---

例如对于这样一个接口：

```java
public interface Connector {

    void connect();

    void disconnect();
}
```

现在想要动态创建一个该接口的实现：接口中的方法被调用时，打印日志：

```java
// 自定义的方法调用处理器
InvocationHandler handler = new InvocationHandler() {
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                System.out.println(method.getName());
                return null;
            }
        };
// 创建Connector的代理对象
Connector connector = (Connector) Proxy.newProxyInstance(ProxyDemo.class.getClassLoader(), new Class[]{Connector.class}, handler);
// 像Connector子类一样调用代理对象的方法
connector.connect();	// 输出connect
connector.disconnect();	// 输出disconnect
```

## 使用CGLIB创建代理对象

---

### 内置代理对象的缺点

Java内置的`Proxy`主要适用于动态创建某个接口的具体实现，创建代理对象时，必须要给出接口，因此，创建某个类的代理对象时，就必须使其继承某个接口，然后基于接口中定义的方法，来进行代理.

当我们需要创建某个类的代理对象时，或者动态创建某个类的子类时，Java内置的代理对象创建工具并不方便.

### CGLIB

`CGLIB`(Code Generation Library) 是一个代码生成库，通过该库提供的工具，可以帮助我们直接创建某个类的代理对象或者动态创建一个类的子类.

`CGLIB`的项目地址：[](https://github.com/cglib/cglib)，使用前，需要先下载相关jar包.

### 使用CGLIB创建代理对象

例如对于这样的类：

```java
public class ApiConnector {
    public void connect() {
        System.out.println("connected");
    }

    public void disconnect() {
        System.out.println("disconnected");
    }
}
```

现在使用`CGLIB`库中提供的方法创建代理对象：

```java
Enhancer enhancer = new Enhancer();
enhancer.setSuperclass(ApiConnector.class);
enhancer.setCallback(new Callback() {});
enhancer.setCallback(new MethodInterceptor() {
    @Override
    public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
        System.out.println(method.getName());
        // 调用原对象的方法
        methodProxy.invokeSuper(o, objects);
        return null;
    }
});
ApiConnector apiConnector = (ApiConnector) enhancer.create();
apiConnector.connect();
apiConnector.disconnect();
// 输出内容：
// connect
// connected
// disconnect
// disconnected
```

不像Java内置的代理对象，使用`CGLIB可以创建类或接口的代理对象，并且在类的代理对象中，还可以方便的调用原对象的方法.且不同于内置的代理对象，`CGLIB`中的代理对象更想一个对象拦截器.