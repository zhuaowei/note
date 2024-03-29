﻿# vim

## 一、基本操作

### 1、模式

1. 普通模式 按ESC切换到此模式
2. 插入模式 按 i
3. 命令模式 按 :
4. 可见模式 按 v

```
# 按 <ESC> 键退出到普通模式
                             i
               /---------------------># 插入模式
              /              /
             /     /-----------------># 命令模式
            /    /           v         
# 普通模式          ------------------># 可视模式
            \    \         <S-V>
             \    \------------------># 行选模式
              \            <C-V>          
               \---------------------># 块选模式
```



### 2、普通模式下：

H: 左

L：右

J：下

K：上

### 3、命令模式

w：保存

q：退出

!：与q连用，强制退出

### 4、帮助文档

命令模式下

显示 w 的帮助

```vim
:help :w
```

按 q 退出指南

文档在 ~/.vimrc 中

### 5、普通模式

w：向前一个单词

b：向后一个按此

d：删除，不能单独使用

dw：向前删除一个单词

dd：删除当前行

u：撤销

<C-R>：重做

### 6、/

进入搜索模式，回车后按 n 可以在结果间切换。

## 二、中级操作

### 1、普通模式

e：移动到单词末尾

0：当行开头

$：当行结尾

<S-6>：当行第一个非空字符处(^)

<C-D>：向下移动一页

<C-U>：向上移动一页

<C-H>：移动到屏幕最顶端

<C-M>：移动到屏幕中心

<C-L>：移动到屏幕最低端

<S-G>：移到文件末尾

gg：移到文件开头

o：在下一行插入一行，并切换到插入模式

<S-O>：在上一行插入一行，并切换到插入模式

### 2、删除

d

可以配合以上命令删除对应字符

c：删除后进入插入模式

cc：删除一整行

x：向前删除单个字符

u：撤销

### 3、快捷键

y：复制（yank）

p：粘贴（paste）

### 4、可视模式：

按 v 进入

进入模式，从此处开始选中字符

<S-V>：行选择模式

<C-V>：进入块选模式。

### 5、数字

8 +  k：商行移动 8 行

di + '/(：删除引号或括号中的左右内容

### 6、分屏

#### 6.1、shell 打开分屏

```shell
vim -On [file1] [file2] ...
vim -on [file1] [file2] ...

# -O 垂直
# -o 水平
# n  分成 n 个
```

#### 6.2、vim 中分屏

```shell
# 1. 创建空白
:new

# 2. 打开当前文件
# 水平
<C-W> s

# 垂直
<C-W> v

# 3. 打开任意文件
# 水平
:split [file]
# 或
:sp [file]

# 垂直
:vsplit [file]
# 或
:vs [file]
```

#### 6.3、关闭分屏

```shell
# 只保留当前分屏
:only
# 或
<C-W> o

# 退出当前分屏
:q
# 或
:quit
```

#### 6.4、分屏快捷键

| 快捷键  | 作用               |
| ------- | ------------------ |
| <C-W> + | 扩大窗口           |
| <C-W> - | 缩小窗口           |
| <C-W> H | 跳转到左边的窗口   |
| <C-W> J | 跳转到下边的窗口   |
| <C-W> K | 跳转到上边的窗口   |
| <C-W> L | 跳转到右边的窗口   |
| <C-W> T | 跳转到最上边的窗口 |
| <C-W> B | 跳转到最下边的窗口 |

