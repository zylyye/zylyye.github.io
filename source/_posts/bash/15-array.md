---
title: Bash 数组
categories: [Bash]
tags: [linux, bash]
date: 2021-03-15 20:29:27
---

---

Bash 中支持定义一维数组，数组中的元素无需连续，也没有数量上限。其可定义的数组类型有两种：**数字索引数组**和**关联数组**（字符串索引数组）。

<!-- more -->

## 创建数组

Bash 中提供了三种创建数组的方式：

1. `arr[index]=value`

```shell
arr[0]=a
arr[1]=b
arr[2]=c
```

2. `arr=(a b c)`

该种方式还可以手动指定索引号：

```shell
arr=(a [3]=b c)  // c 的索引号是 4，紧接着 b 之后
```

还可以使用扩展符，例如自动扩展成文件列表：

```shell
txts=( *.txt )
```

3. 使用 declare 声明：

```shell
declare -a arr
```

## 数组的访问

数组访问时，必须在 `${}` 中，并指定索引号：

```shell
echo ${arr[1]}
```

**如果没有指定索引号，则返回 arr[0] 的值**，设置元素时，如果没有使用索引，也默认索引为 0：

```shell
declare -a arr
arr=1 // 相当于 arr[0]=1
```

### 读取所有元素

- 可以使用 `@` 或 `*` 获取数组的所有元素：

```shell
arr=(a b c)
echo ${arr[@]} // a b c
```

- 可以配合 `for..in` 循环遍历数组：

```shell
for a in "${arr[@]}"; do
  ...
done
```

**注意，遍历数组时，建议将数组放到 `""` 中，避免数组中的元素内含有空格，导致该元素被当做空格间隔的多个元素遍历。**

例如：

```shell
arr=("a b" c) // 两个元素
for a in ${arr[@]}; // 相当于 for a in a b c;
for a in "${arr[@]}"; // 相当于 for a in "a b" "c"
```

- 可以使用此方式复制、添加数组:

```shell
new_arr=("${arr[@]}")
new_arr=("${arr[@]}" 123)
```

### 数组的长度

获取数组长度的方法：

```shell
echo ${#arr[@]}
# or
echo ${#arr[*]}
```

获取的数组长度是有值的元素个数（包含空字符串），注意，在其他 SHELL 中（例如 zsh），可能表现的不一致。

### 取数组索引列表

获取数组中有值得数据索引号：

```shell
echo ${!arr[@]} // 索引号空格隔开，形如 2 4 5
```

可以配合 `for..in` 循环遍历数组：

```shell
for i in ${!arr[@]}; do
  echo ${arr[i]}
done
```

## 数组的截取

可以使用索引号，截取数组中的一部分元素内容：

```shell
echo ${arr[@]:position:length}  // 从 position 位置的元素开始（包含），截取 length 个元素
```

如果不指定 length，则表示截取到数组末尾：

```shell
echo ${arr[@]:3:4} // 从索引 3 开始，截取 4 个元素
echo ${arr[@]:3} // 从索引 3 开始，截取到末尾
```

## 追加数组元素

可以使用 `+=` 在数组末尾增加元素：

```shell
arr=(1 2 3)
arr+=(4 5 6)
echo ${arr[@]} // 1 2 3 4 5 6
```

## 删除数组元素

可以使用 `unset` 命令删除元素中的成员：

```shell
unset arr[1]
// 等价于
arr[1]=
```

清空整个数组可以直接 `unset arr`

## 关联数组

关联数组即是用字符串当做索引的数组：

```shell
declare -A arr
arr['name']='My Name'
arr['age']='18'
```

使用关联数组是，必须显示的使用 `declare -A` 进行声明：

```shell
declare -A arr
```

赋值方式有两种：

```shell
arr['name']='My Name'
arr['age']='12'
# 或者
arr=([name]='My Name' [age]='12')
```

---

