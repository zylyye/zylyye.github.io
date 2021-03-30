---
title: Bash 启动环境
categories: [Bash]
tags: [linux, bash]
date: 2021-03-30 20:29:30
---

---

Bash 启动时会通过脚本来初始化环境，有关的启动初始化脚本有两种：1. profile 2. bashrc 。

<!-- more -->

## Session

Bash 启动时会新建会话 (session)，会话分为两种：

1. 登录 session
2. 非登录 session

启动使用的初始化脚本与 session 类型有关。

### 登录 session

用户登录系统新建的 session 是登录 session，此时 Bash 初始化脚本 profile，其初始化顺序如下：

1. /etc/profile  全局脚本，所有用户登录时都会执行
2. /etc/profile.d 下的脚本，一般以 `.sh` 结尾，全局脚本，**通常自定义的初始化脚本放在这里**
3. ~/.bash_profile  用户的个人配置脚本。**该脚本存在，则不再往下执行。**
4. ~/.bash_login  如果 ~/.bash_login 不存在，执行该脚本。**该脚本存在则不再往下执行。**
5. ~/.profile 最后一个尝试执行的脚本

bash 命令可以使用 `--login` 选项执行 profile 脚本。

### 非登录 session

用户登录后，手动打开 Bash 新建的 session 是非登录 session，非登录 session 使用 bashrc 会使用 bashrc 进行脚本初始化，顺序如下：

1. /etc/bash.bashrc  全局有效
2. ~/.bashrc  只对当前登录用户有效

## .bash_logout

Bash 退出时会执行 ~/.bash_logout 脚本

## 键盘绑定

可以在 /etc/inputrc 和 ~/.inputrc 文件里定义快捷键：

```shell
# Ctrl + t 触发 pwd 命令
"\C-t":"pwd\n"
```

---

