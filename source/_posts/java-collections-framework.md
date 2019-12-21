---
title: Java集合框架
categories: []
tags: []
date: 2018-07-27 22:44:22
---



Java集合框架对常用的算法、数据结构进行了封装，组成了一套可以用于多种处理对象集合场景的框架.

<!-- more -->

## 集合框架中的接口

---

Java集合框架中为不同类型的集合定义了一个接口，并根据这个接口，提供了多种实现，几乎所有的集合接口或类都支持泛型，集合框架中定义了两大基础接口：`Collection`与`Map`

集合框架中所有的集合类都实现自这两个接口，其中`Collection`下包含了一以下几个核心接口：

- `Set` - 一个没有重复元素集合
  - `SortedSet` - 有序存储集
- `List` - 有序列表
- `Queue` - 队列
- `Deque` - 双端队列

`Map`中包含了以下核心接口：

- `Map` - 包含键值对的映射
  - `SortedMap` - 按照键进行有序存储的映射

把功能抽象为接口与实现分离的设计使得这些集合类充分享有多态带来的灵活性，例如常见的：

```java
List<String> strList = new ArrayList<>();
```

如果之后希望换成其他的数据结构的列表(例如链表结构的列表)，则可以直接替换，而无需担心影响已经使用了该对象的语句：

```java
List<String> strList = new LinkedList<>();
```

官方文档给出了一个不同接口及实现的概览表格：

| Interface | Hash Table                                                   | Resizable Array                                              | Balanced Tree                                                | Linked List                                                  | Hash Table + Linked List                                     |
| :-------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `Set`     | [`HashSet`](https://docs.oracle.com/javase/10/docs/api/java/util/HashSet.html) |                                                              | [`TreeSet`](https://docs.oracle.com/javase/10/docs/api/java/util/TreeSet.html) |                                                              | [`LinkedHashSet`](https://docs.oracle.com/javase/10/docs/api/java/util/LinkedHashSet.html) |
| `List`    |                                                              | [`ArrayList`](https://docs.oracle.com/javase/10/docs/api/java/util/ArrayList.html) |                                                              | [`LinkedList`](https://docs.oracle.com/javase/10/docs/api/java/util/LinkedList.html) |                                                              |
| `Deque`   |                                                              | [`ArrayDeque`](https://docs.oracle.com/javase/10/docs/api/java/util/ArrayDeque.html) |                                                              | [`LinkedList`](https://docs.oracle.com/javase/10/docs/api/java/util/LinkedList.html) |                                                              |
| `Map`     | [`HashMap`](https://docs.oracle.com/javase/10/docs/api/java/util/HashMap.html) |                                                              | [`TreeMap`](https://docs.oracle.com/javase/10/docs/api/java/util/TreeMap.html) |                                                              | [`LinkedHashMap`](https://docs.oracle.com/javase/10/docs/api/java/util/LinkedHashMap.html) |

其中`Deque`是双端队列，是`Queue`的子类.

## 抽象集合类

---

集合框架中定义了几个实现基本接口的抽象类：

- AbstractCollection
- AbstractSet
- AbstractList
- AbstractSequentialList
- AbstractQueue
- AbstractMap

这些抽象类给出了接口的基本实现，方便自定义实现集合类.

## 可选的方法与标记接口

---

### 可选的方法

集合接口中定义的修改集合的方法不强制子类必须实现以提供支持(例如add, remove, clear)，子类在实现集合接口时，可以根据需要在实现了这些接口的方法中抛出`UnsupportedOperationException`以表示当前的实现类不支持该操作.

例如`Arrays.asList()`方法返回的`List`是一个不可修改的列表，调用其`add()`方法时，会抛出`UnsupportedOperationException`异常.

### `RandomAccess`标记接口

集合框架中使用`RandomAccess`接口来标记当前集合类支持高效的随机访问.

例如`ArrayList`类即有这个接口，因为`ArrayList`内部是使用数组来存储对象的，因此，可以支持使用存储索引来高效随机访问，但是`List`的另一种数据结构的实现`LinkedList`就不能高效的进行随机访问，因为其内部对象的存储基于双向链表结构，要想访问指定索引出的对象，必须对链表中的各个节点进行顺序遍历.

## `Collection`接口

---

`Collection`接口是所有集合的基础接口，其中定义了操作集合的基础方法：

```java
// 返回集合的大小
int size();

// 判断集合是否为空
boolean isEmpty();

// 查询集合中是否包含指定元素
boolean contains(Object o);

// 返回一个集合元素迭代器
Iterator<E> iterator();

// 向集合中添加新元素
boolean add(E e); 

// 删除集合中指定的元素
boolean remove(Object o);
```

### 遍历集合

有四种用来遍历集合中元素的方式：

- 使用聚合操作（自Java8起提供的stream流来操作集合）
- 使用for-each结构语句
- 使用迭代器

#### 聚合操作(stream流)

自Java8起，可以通过stream流来对集合进行聚合操作，`Collection`接口中定义了将集合转换为stream流的方法：

```java
// 返回一个stream流
Stream<E> stream();

// 返回一个stream并行流
Stream<E> parallelStream()
```

例如对一个字符串集合进行筛选联结操作：

```java
List<String> strList = Arrays.asList("aaa", "a", "b", "c");
String result = strList.stream()
        .filter(str -> str.length() == 1)
        .collect(Collectors.joining(","));
System.out.println("result = " + result);
// 
```

#### 使用for-each结构语句

`Collection`实现了`Iterable`接口，对于实现了`Iterable`接口的类，可以使用for-each结构语句对其进行遍历：

```java
// 遍历列表中的字符串并打印
for (String content : list) {
    System.out.println(content);
}
```

`Iterable`中还定义了`forEach()`方法用来对集合进行遍历，不过该方法的内部同样是使用for-each结构语句实现.

#### 使用迭代器

调用集合中的`iterator()`会返回一个迭代器，迭代器接口`Iterator`中定义了如下的基础方法：

```java
// 判断是否还有待迭代的元素
boolean hasNext();

// 返回下一个元素，如果没有，则抛出NoSuchElementException
E next();

// 移除刚才迭代的元素，该方法在每次迭代后，只能调用一次
void remove();

// 处理剩余的元素
forEachRemaining(Consumer<? super E> action);
```

迭代器典型的使用方式如下：

```java
Iterator<String> iterator = list.iterator();
while (iterator.hasNext()) {
    String nextStr = iterator.next();
    System.out.println(nextStr);
}
```

使用迭代器筛选字符串列表：

```java
List<String> list = new ArrayList<>(Arrays.asList("aaa", "a", "b", "c"));
Iterator<String> iterator = list.iterator();
while (iterator.hasNext()) {
    String str = iterator.next();
    if (str.length() != 1) {
        iterator.remove();
    }
}
System.out.println("list = " + list);
// list = [a, b, c]
```

**注意与使用stream流进行筛选的方式不同的是，使用迭代器删除会更改原始集合，使用stream流进行筛选不会对原集合产生副作用.**

默认情况下，迭代器只能向后迭代，不过`List`中可以使用`listIterator()`方法来获取一个列表迭代器，该迭代器支持向前迭代.

### 批操作方法

`Collection`接口中定义了一些有用的批操作方法接口：

```java
// 判断集合中是否包含指定集合中的全部元素
boolean containsAll(Collection<?> c);

// 将指定集合中的元素添加到本集合中
boolean addAll(Collection<? extends E> c);

// 移除包含在指定集合中的全部元素
boolean removeAll(Collection<?> c);

// 给定一个选择器，移除全部符合条件的元素
boolean removeIf(Predicate<? super E> filter);

// 只保留和指定集合中相等的元素(取两集合交集)
boolean retainAll(Collection<?> c);

// 清除集合中的所有元素
void clear();
```

例如移除集合中为`null`的元素：

```java
list.removeAll(Collections.singleton(null));
// 或
list.removeIf(Objects::isNull);
```

### 数组相关方法

集合接口中定义了将集合转化为数组的方法：

```java
// 转化为Object数组
Object[] toArray();

// 转化为指定类型数组，集合顺序填充数组a，剩余的填充null，如果a的长度不够，则创建一个新的数组填充返回
<T> T[] toArray(T[] a);
```

例如将字符串列表转化为字符串数组：

```java
String[] strings = list.toArray(new String[0]);
```

## `Set`接口

---

`Set`在是一种不包含重复元素的集合，`Set`并没有在`Collection`的基础上增加新的方法，只是在新增时限制不能插入重复元素，利用该特性，可以为一个集合去重：

```java
Set<String> uniList = new HashSet<>(list);
// 或者使用stream流，一下语句返回一个HashSet实例
Set<String> uniList = list.stream().collect(Collectors.toSet());
```

此外`Set`中重写了`equals()`方法，当两个`Set`实例之间进行比较时，只要两者包含相同的元素(即使顺序不同)，则视为相等.

### 基本实现类型

`Set`接口下主要包含了三种实现：

- `HashSet` - 最常用、性能最佳的实现，内部使用哈希表存储，元素无序
- `TreeSet` - 使用红黑树结构存储，元素有序存储
- `LinkedHashSet` - 使用链表结构存储，元素按照插入时的顺序存储

`TreeSet`始终会将元素维持为一个有序的树状结构(红黑树，一种自平衡二叉树)，可以实现高效的有序插入、删除操作.

`LinkedHashSet`内部使用链表结构的哈希表存储，可以保持元素插入的顺序(注意如果插入已存在的元素不会影响之前元素的存储顺序，因为`Set`只会插入集合中不存在的元素)，如果要对一个有序列表进行去重，则可以使用`LinkedHashSet`，以保持有序列表中的位置：

```java
Set<String> uniList = new LinkedHashSet<>(sortedList);
```

## `List`接口

---

`List`列表是一种有序集合，与`Set`不同，列表中可以包含重复元素.此外，除了实现了集合中的基本接口外,列表中还额外添加了一些有用的功能方法:

- 位置访问

  列表中可以直接使用索引来访问元素,列表接口中定义了以下方法:

  ```java
  // 获取指定索引处的元素
  E get(int index);
  
  // 设置指定索引除的元素,并返回之前的元素
  E set(int index, E element);
  
  // 移除并返回指定索引处的元素
  E remove(int index);
  ```

  `List`有基于数组和基于链表两种数据结构的实现,基于数组的实现可以高效的进行位置访问操作, 如`ArrayList`, 而基于链表的实现, 访问指定索引出的元素时, 需要进行顺序遍历, 因此效率较低, 例如`LinkedList`

- 位置搜索

  列表中可以根据给定元素, 确定其在列表中的位置:

  ```java
  // 返回指定元素第一次和最后一次出现的位置,没有则返回-1
  int indexOf(Object o);
  int lastIndexOf(Object o);
  ```

- 列表迭代器

  与集合中默认的迭代器不同, 列表中的迭代器可以向前遍历, 列表中可以用以下方法获取一个列表迭代器:

  ```java
  // 返回一个列表迭代器, 可以指定起始位置
  ListIterator<E> listIterator();
  ListIterator<E> listIterator(int index);
  ```

- 范围视图

  列表可以返回一个范围内的列表视图, 列表返回的视图支持列表的所有操作:

  ```java
  // 返回指定范围的子列表
  List<E> subList(int fromIndex, int toIndex);
  ```

  对`sublist()`方法返回的列表视图进行修改会直接影响到源列表, 由此,可以结合列表的其他方法来简化工作, 例如删除列表中的某个范围内的元素:
  
  ```java
  list.subList(1, 5).clear();
  ```
  
### 基本实现类型

合框架中的`List`有两种基本实现:

- `ArrayList` - 最常用的实现, 内部基于数组实现, 可以提供高效的随机访问与遍历
- `LinkedLisk` - 内部基于链表结构, 可以提供高效的删除/插入操作

## `Queue`接口

---

`Queue`是一个队列, 用于保存待处理的元素集合, 队列接口在集合的基础上增加了额外的增删/获取方法, 方法作用及特点如下(表中只有`add()`不是新方法):

|      操作类型      | 失败时方法抛出抛出异常 | 失败时返回特殊值 null/false |
| :----------------: | :--------------------: | :-------------------------: |
|      插入队列      |         add(e)         |          offer(e)           |
|  获取并从队列移除  |        remove()        |           poll()            |
| 获取但不从队列移除 |       element()        |           peek()            |

当队列为空时, 调用获取元素的方法会返回null, 因此, 一般队列的队列的实现中限制了不能存放null(`LinkedList`没有这个限制, 它允许插入null), 

### 队列元素的顺序

 `Queue`的一般实现都是一个FIFO(first in firt out, 先入先出)对列, 队列新增的元素会放置到队列的末尾, 需要取出元素时, 会从返回队列头部的元素. 不过也有可以指定排序规则的队列, 例如`PriorityQueue`, 创建该队列时, 可以指定一个比较器(Compartor), 这样队列中的元素将按照指定的顺序排序. 

### 有界队列

在`java.util`包中的队列实现都是无界的, `java.util.concurrent`包中的实现是有界的, 该包中的存放的都是线程安全的实现.

## `Deque`接口

`Deque`是一个双端队列, 与`Queue`队列不同的是, 双端队列可以在队列的两端进行元素的插入和删除操作, 其提供的操作队列的功能与`Queue`一致, 不过分为操作队首和队尾两个版本:

|             操作             | 失败抛出异常                      | 失败返回false/null              |
| :--------------------------: | --------------------------------- | ------------------------------- |
|        插入队首或队尾        | addFirst(e)<br />addList(e)       | offerFirst(e)<br />offerLast(e) |
|    获取并从队首或队尾删除    | removeFirst(e)<br />removeLast(e) | pollFirst(e)<br />pollLast(e)   |
| 获取队首或队尾的元素但不删除 | getFirst()<br />getLast()         | peekFirst()<br />peekLast()     |

除了这些基本的操作队列方法外, `Deque`中还提供了其它有关从队列中移除元素的方法:

```java
// 从队列中移除第一个或最后一个出现的指定元素, 如果移除成功则返回true, 否则返回false
boolean removeFirstOccurrence(Object o);
boolean removeLastOccurrence(Object o);
```

## `Map`接口

---

`Map`不是属于集合, 它是一个存放键值对的映射表, 映射表中的键不会重复, 因此同一个键值对, 映射表中最多只会存在一个, 此外, 与队列不同, `Map`中的键值都可为null,  `Map`接口中定义了有关映射表的基础方法:

```java
// 存放指定键值对
V put(K key, V value);

// 使用键获取指定的值, 没有则返回null
V get(Object key);

// 获取映射表中指定键对应的值, 没有则使用指定的默认值
V getOrDefault(Object key, V defaultValue)

// 删除指定的键值
V remove(Object key);

// 获取映射表中键值对的数量
int size();

// 判断表映射是否为空
boolean isEmpty();

// 判断映射表中是否包含指定的键
boolean containsKey(Object key);

// 判断映射表中是否包含指定的值
boolean containsValue(Object value);
```

### `Map`的基本实现类型

`Map`接口的基本实现类型及特点:

- `HashMap` - 最常用, 最高效的hash表实现, 键值无序存放
- `TreeMap` - 键有序存放, 内部是红黑树结构
- `LinkedHashMap` - 键值使用双向链表存放, 可以保持键的插入顺序

### 批操作接口

`Map`中定义了与集合类似的批操作接口:

```java
// 复制指定映射表中的所有键值对到当前映射表
void putAll(Map<? extends K, ? extends V> m);

// 清空映射表
void clear();
```

`putAll()`方法通常用来合并一个映射表, 如果只是想简单的复制一个映射表, 直接使用构造方法即可:

```java
Map<String> mapCopies = new HashMap(map);
```

### 集合视图

`Map`接口中定义了三个与集合视图相关的方法, 分别返回三种视图:

```java
// 返回键的集
Set<K> keySet();

// 返回值的集合
Collection<V> values();

// 返回键值对的集
Set<Map.Entry<K, V>> entrySet();
```

#### 集合视图的巧妙用处

`Map`对象返回的集合视图是可以修改的, 这些修改会直接影响到原有的映射表, 例如:

- 删除指定的键:

    ```java
    map.keySet().removeAll(keyList);
    ```

- 保留两个映射表共同键的部分:

    ```java
    map1.keySet().retainAll(map2.keySet());
    ```

- 删除表中指定的值:

    ```java
    map.values().removeAll(Collections.singleton(specValue));
    ```

- 删除表中键和值相等的数据:

    ```java
    map1.keySet().removeAll(map1.values());
    ```

此外, 可以使用集合视图来遍历映射表:

```java
for (Map.Entry<String, Integer> entry : map.entrySet()) {
    String key = entry.getKey();
    Integer value = entry.getValue();
    // ...
}
```

需留意, 调用Map对象获取视图的方法时, Map对象中会缓存创建的视图, 如果多次调用, 返回的将是同一个视图对象, 即:

```java
map.keySet() == map.keySet(); // true
map.values() == map.values();  // true
map.entrySet() == map.entrySet();  // true
```



## 集合框架实现类

### 集`Set`

`Set`集中不会包含重复的元素，`Set`中最多只能有一个`null`元素

#### 线程不安全的`Set`

- `HashSet`
  - `LinkedHashSet`
- `SortedSet`
  - `NavigableSet`
    - `TreeSet`
- `EnumSet`

#### 线程安全的`Set`

- `ConcurrentSkipListSet`
- `CopyOnWriteArraySet`

### 列表`List`



- `ArrayList`
- `LinkedList`
- `CheckList`
- `Vector`
- `Stack`



### 队列`Queue`与双端队列`Deque`



- `BlockingQueue`与`BlockDeque`

  

## 常用映射类说明

### 线程不安全的`Map`

- `HashMap`
- `LinkedHashMap`
- `IdentityHashMap`
- `WeakHashMap`
- `SortedMap`
- `TreeMap`
- `EnumMap`
- `HashTable`

### 线程安全的`Map`

- `ConcurrentMap`
- `ConcurrentHashMap`
- `ConcurrentNavigableMap`

  

## 参考文档

---

1. <https://docs.oracle.com/javase/tutorial/collections/index.html>
2. [API文档](https://docs.oracle.com/javase/10/docs/api/java/util/package-summary.html)