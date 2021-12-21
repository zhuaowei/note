# shell基础

## 一、常用定义

﻿### 1、变量

```shell
foo=bar
# 空格很关键，不能随便加空格

foo="hello world"
foo=hello\ world
# 可以用双引号括起来或用转义字符
```

### 2、脚本

```
$0 脚本名
$1 - $9 脚本参数
$@ 所有参数
$# 参数个数
$? 上个命令的返回值
$$ 当前命令的pid
!! 上一条命令包括参数
$_ 上一条命令的最后一个参数
```

source

### 3、短路

同 C 语言

```
false && true
true || false
// 后面的语句不会执行
```

用 $ 调用变量

双引号格式化输出

单引号原样输出

```shell
echo "我们在$(pwd)"

echo '我们在$(pwd)'
```

### 4、重定向

将命令包装成文件

```shell
cat <(ls)
```

### 5、test 命令

```shell
test 1<2
[ 1 < 2 ]
```


### 6、释伴

```python
#!/usr/local/bin/python
# 第一行注释告诉程序，在何处调用程序
```

如果不知道程序位置，或在不同设备程序位置不同

```python
#!/usr/bin/env python
# 查找 python 位置
```

### 7、shellcheck

检查脚本，提出建议

### 8、查找文件

find

locate

全盘查找，以数据库方式

```shell
sudo updatedb
```

### 9、tree

树状显示文件

### 10、history

查找历史命令

### 11、插件

zsh

复杂

oh my zsh

好用

auto jump

自动跳转文件

## 二、shell多任务控制

### 1、任务控制

Ctrl + c ：打断任务

> 程序运行时，发送SIGINT信号给程序，程序可以选择执行信号

ctrl + \：打断任务 SIGQUIT

Ctrl + z：挂起/暂停程序 SIGSTOP

```shell
jobs
查看当前挂起程序

fg %1
1 是任务编号，放在前台运行

bg %1
后台运行

kill %1
杀掉程序

kill -STOP %1
暂停程序

kill -HUP %1
挂起程序
终端被关闭时，终端会向所有词此窗口创建的子程序发送 SIGHUP

nohup sleep 1000 &
& 直接在后台运行
nohup 忽略信号

ps aux
ps 列出用户运行的程序
aux 所有用户运行的程序
```

### 2、工具 tmux

需要安装

1. 使用tmux命令

```shell
tmux
```

2. deteach

Ctrl + b，d

3. 退出

exit

4. 创建新窗口

Ctrl + b，c

5. 切换窗口

Ctrl + b，数字

6. 分屏

Ctrl + b，%

7. 分屏后切换

Ctrl + b，左右箭头

8. 上下分屏

Ctrl + b，shift + ‘

9. 放大/缩小窗口

Ctrl + b，z

10. 切换布局

Ctrl + b，space

- 给会话起名

```shell
tmux new -s <session-name>
```

- 将当前窗口分离

Ctrl + b，d 或

```shell
tmux detach
```

- 查看tmux窗口

```shell
tmux ls
```

- 接入会话

```shell
tmux attach -t 0

tmux attach -t <session-name>
```

- 杀死会话

```shell
tmux kill-session -t 0

tmux kill-session -t <session-name>
```

- 切换会话

```shell
tmux switch -t 0

tmux switch -t <session-name>
```

- 重命名会话

```shell
tmux rename-session -t 0 <new-name>
```

- 会话快捷键
  * ctrl + b，d：分离当前会话
  * ctrl + b，s：列出当前所有会话
  * ctrl + b，$：重命名当前会话

- 一般流程

1. 新建会话

```shell
tmux new -s my_session
```

2. 运行所需程序
3. 将会话分离
4. 下次使用时重新连接到会话

```shell
tmux attach-seesion -t my_session
```

- 暂时不写，太多

> http://www.ruanyifeng.com/blog/2019/10/tmux.html

### 3、配置文件

1、alias

起别名

```shell
alias ll="ls -alh"

ll

alias mv="mv -i"
mv ./1.txt ./2.txt
```

> 只对当前窗口有效

2、.bashrc或.zshrc或.vimrc文件
