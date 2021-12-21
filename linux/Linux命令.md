﻿# Linux命令

## 一、基础命令

﻿### 1、重定向

echo：会在**终端**显示参数指定的文字。

重定向：> 和 >>

> **>**: 输出，会覆盖原有内容
>
> **>>**  :  追加到末尾

### 2、管道

管道 ：|

例： ls -lh ~| more

### 3、关机、重启

远程管理相关命令

关机：shutdown （选项）（时间）

不加选项，默认一分钟后关机。

重启：shutdown -r (时间)

取消重启：shutdown -c

### 4、IP配置

查看或配置网卡信息（IP地址）：ifconfig

检测目标IP地址的连接是否正常：ping ip地址

### 5、SSH

#### 5.1、常见服务默认端口

域名：IP地址的别名 -- www.XXXX.com （方便记忆）

端口号：通过端口号可以找到计算机上运行的程序

SSH服务器默认端口号是 22

Web 服务器  80

HTTPS   443

FTP  21

#### 5.2、SSH远程登录

SSH使用

ssh [-p port] user@remote

user : 远程机上的用户名，不指定默认为当前用户

remote ：远程机的地址

port : SSH Server 监听的窗口

使用 exit 退出当前用户登录

#### 5.3、远程拷贝

scp：远程拷贝

#将本地文件复制到远程机

scp -**P** port 文件名 user@remote**:**指定目录/文件名

> 以 Home 作为相对路径

将远程机的文件复制到本机

```shell
scp -P port user@remote:<dir>/<file> <file>
```

#### 5.4、复制目录

> scp -**P** port **-r** 文件名 user@remote**:**指定目录/文件名

> scp -**P** port **-r** user@remote**:**指定目录/文件名 文件名

FileZile 使用 FTP 协议进行文件传输

端口号：21 ？？？

#### 5.5、第一次登陆

第一次登陆远程机有提示，以后没有。

连接过的远程机会被记录到 ~/.ssh/ 中

#### 5.6、ssh配置文件

有关 SSH 配置信息都保存在用户家目录下的 .ssh 目录下

#### 5.7、免密登录

​	1、配置公钥：执行 ssh-keygen 即可生成 SSH 公钥，一路回车即可

生成两个文件，带 pub 为公钥，另一个为私钥。

​	2、上传公钥到服务器：执行命令 ssh-copy-id (-p port) user@ip

上传到服务器的为公钥。在服务器的 .ssh/ 文件夹中。

#### 5.8、配置别名

​	1、在本机  ~/.ssh/ 下创建 config 文件

​	2、编辑 config 文件，命令：gedit config

​	3、配置

> Host 别名 （不能有空格）
>
> ​	HostName IP地址
>
> ​	User 用户名
>
> ​	port 22

远程拷贝目录1下文件到目录2：

> scp -r 目录1 别名:目录2

### 6、用户权限相关

##### 6.1、权限说明

<table>
    <tr>
        <th align="center">序号</th>
        <th align="center">权限</th>
        <th align="center">英文</th>
        <th align="center">缩写</th>
        <th align="center">数学代号</th>
    </tr>
    <tr>
        <td align="center">01</td>
        <td align="center">读</td>
        <td align="center">read</td>
        <td align="center">r</td>
        <td align="center">4</td>
    </tr>
    <tr>
        <td align="center">02</td>
        <td align="center">写</td>
        <td align="center">write</td>
        <td align="center">w</td>
        <td align="center">2</td>
    </tr>
    <tr>
        <td align="center">03</td>
        <td align="center">执行</td>
        <td align="center">execute</td>
        <td align="center">x</td>
        <td align="center">1</td>
    </tr>
</table>

##### 6.2、组

设置组的权限，将人发分到组中

第 1 位：-  是文件；d  是目录

2 ~ 4位：对应用户（第三列）

5 ~ 7位：对应组 （第四列）

8 ~ 10位：对应其他用户

##### 6.3、硬链接数（第二列）

有多少种方式打开文件（夹）

##### 6.4、chmod 简单使用 （重要）

chmod ：可以修改 用户/组 对 文件/目录 的权限

命令：

> chmod +/-rwx 文件名/目录名

如果目录的可执行权限（x）被去掉，目录就失去了所有权限，

不能打开，也不能查看目录下的文件。

目录被去掉可读权限，可以打开目录，无法查看目录下的文件。

目录被去掉可写权限，可以查看文件，无法创建文件。

##### 6.5、超级账户 root

对所有文件有所有访问权限

大多数不推荐直接使用 root 账号登录系统

通常使用的是“标准用户”

sudo ：表示使用另一个用户的身份执行命令，预设身份为 root。

使用sudo必须输入密码，五分钟内有效。

### 7、组管理

**创建/删除组** 的终端命令都需要通过 **sudo** 执行

作用															命令

添加组														groupadd 组名

删除组														groupdel 组名

确认组信息   											cat /etc/group

递归修改文件/目录的所属组			  chgrp -R 组名 文件/目录

##### 7.1、创建用户/设置密码/删除用户

1 > 添加新用户：

```shell
useradd -m -g 组名 新建用户名
```

< -m ：自动建立用户家目录 >

< 如果忘记 -m ，最简单的就是删除此用户，重新创建 >

< -g ：指定用户所在的组，否则会建立一个和用户同名的组 >

2 > 设置用户密码

> passwd 用户名

< 如果是普通用户，直接用 passwd 可以修改自己的账户密码 >

3 > 删除用户

> userdel -r 用户名

< -r 选项会自动删除用户家目录>

4 > 确认用户信息

> cat /etc/passwd | grep 用户名

< 新建用户后，用户信息会保存在 /etc/passwd 文件中 >

##### 7.2、查看用户信息

1 > 查看用户 UID 和 GID 信息

> id [用户名]

2 > 查看当前所有登录的用户列表

> who

3 > 查看当前登录用户的账户

> whoami

4> 设置用户的 主组/附加组 和登录 Shell

主组 ：新建用户时，用 -g 命令建立的组

在 etc/passwd 的第四列GID 对应的组

附加组：在 etc/group 中最后一列表示该组的用户列表，

用于指定用户的附加权限

> 设置了用户的附加组后，需重新登录才能生效！

```shell
# 修改用户的主组（passwd 中的 GID）
usermod -g 组 用户名

# 修改用户的附加组
usermod -G 组 用户名

```

> 注意：默认使用 useradd 添加的用户是没有 sudo 以及 root身份执行命令的，
>
> 可以使用以下命令，将用户添加到附加组中

```shell
usermod -G 用户
```

#### 8、Shell

登录使用的 Shell，就是登陆之后，使用的终端命令，**ubuntu** 默认是 **dash**

```shell
# 修改用户登录 Shell
usermod -s /bin/bash
```

#### 9、which

> - /etc/passwd  是用于保存用户信息的**文件**
> - /usr/bin/paawd  是用于修改用户密码的**程序**

- which 命令可以查看执行命令所在位置

```shell
which ls

# 输出
# /bin/ls 

which useradd

# 输出
# /usr/sbin/useradd
```

bin 和 sbin

- 在 Linux 中，绝大多数可执行文件都是保存在 /bin、/sbin、/usr/bin、/usr/sbin 中
- /bin 二进制执行文件目录，主要用于具体应用
- /sbin 系统管理员专用的二进制代码，主要用于系统管理
- /usr/bin 后期安装的软件
- /usr/sbin 超级用户的一些管理程序

切换用户

```shell
#切换用户， 并切换目录。 - 可以切换到用户家目录，否则保持位置不变
su - 用户名

#退出当前登录账户
exit
```



su  不接用户名，可以切换到 root，但是不推荐使用，因为不安全

exit 

```
python ------->  zhnangsan ------> lisi
	|  \              |  \           |
	|     \           |      \       |
 	|        \        |          \   |
exit shell          exit            exit
```

#### 10、修改用户权限

```shell
# 修改拥有者
chown

# 修改组
chgrp

# 修改权限
chmod
```

命令格式

```shell
# 修改文件/目录的拥有者
chown 用户名 文件名/目录名

# 递归修改文件/目录的组
chgrp -R 组名 改文件/目录

# 递归修改文件权限
chmod -R 755 文件/目录
```

r 可读			4

w 可写		   2

x 可执行		1

- chmod 在设置权限时，可以简单地使用三个数字分蘖对应拥有者/组 和 其他用户的权限

```shell
# 直接修改文件/母的 读/写/执行 权限，但是不能精确到 拥有者/组/其他
chmod +/-rwx 文件名/目录名
```

### 11、系统信息

1. 时间和日期

```shell
# 查看系统时间
date

# calendar 查看日历， -y 选项可以查看一年的日历
cal
```

2. 磁盘信息

```shell
# disk free 显示磁盘剩余空间
df -h

# disk usage 显示目录下的文件大小
du -h [目录名]
```

> -h 以人性化的方式显示文件大小

3. 进程信息

- 所谓进程，通俗的说就是当前正在执行的一个进程

```shell
# process status  查看进程的详细状况
ps aux

# 动态显示运行中的进程，并且排序
top

# 终止指定代号的进程， -9 表示强行终止
kill [-9] 进程代号
```

> ps 默认只会显示当前用户通过终端启动的应用进程

- ps 选项功能说明

```
a 显示终端上的所有进程
u 显示进程的详细状态
x 显示没有控制终端的进程
```

> 提示：使用 kill 命令时，最好只终止由当前用户开启的进程，而不要终止 root 身份开启的进程，否则可能导致系统崩溃

- 要退出 top ，可直接输入 q

## 二、进阶命令

### 1、查找文件

- find 命令功能非常强大，通常用来在特定的目录下搜索符合条件的文件

```shell
# 查找指定路径下扩展名是 .py 的文件，包括子目录
find [路径] -name "*.py"
```

- 如果省略路径，表示在当前文件夹下查找
- 之前学习的通配符，在使用 find 时同时可用

### 2、软连接

 类似windows下的快捷方式

```shell
# 建立文件的软链接
ln -s 被链接的源文件 链接文件
```

> 注意：
>
> 1、没有 -s 建立的是一个硬链接
>
> 2、两个文件占用相同大小的硬盘空间，工作中几乎不会建立文件的硬链接
>
> 3、源文件要使用绝对路径，不能使用相对路径，这样可以方便移动链接文件后，仍然能够正常使用

硬链接（了解）

```
# 软连接
软连接文件数据 <-------- 软连接文件名
			\
			  \
			    \
文件数据 <--------- 文件名

# 硬链接
			  /--- 文件名
			/
文件数据 <--
			\
			  \--- 硬链接
```

Linux 中，文件名与文件数据是分开储存的。

- 只有硬连接数为0，文件才能被删除

3. #### 打包压缩

- 不同系统中，常用打包压缩方式是不同的

> Windows 常用 rar
>
> Mac 常用 zip
>
> Linus 常用 tar.gz

3.1 打包/解包

- tar 是Linux 中最常用的 备份工具，此命令可以把一系列文件打包到 一个大文件中，也可以把一个打包的大文件恢复成一系列文件
- tar 的命令格式如下

```shell
# 打包文件
tar -cvf 打包文件.tar 被打包的文件/路径

# 解包文件
tar -xvf 打包文件.tar
```

- tar 选项说明

```
c 生成档案文件，创建打包文件
x 解开档案文件
v 列出归档接档的详细过程，显示进度
f 指定档案文件名称， f 后面一定是 .tar 文件，所以必须放在选项最后
```

3.2 压缩/解压缩

1、gzip

- tar 与 gzip 命令结合可以实现打包和压缩
- tar 只负责打包文件
- 用 gzip 压缩 tar打包后的文件，其扩展名一般为 xxx.tar.gz

> 在 Linux 中，最常见的压缩文件格式就是 xxx.tar.gz

- 在 tar 命令中有一个选项 -z 可以调用 gzip ，从而可以方便的实现压缩和解压缩的功能
- 命令格式如下

```shell
# 压缩文件
tar -zcvf 打包文件.tar.gz 被压缩的文件/路径

# 解压缩文件
tar -zxvf 打包文件.tar.gz

# 解压缩文件到指定路径
tar -zxvf 打包文件.tar.gz -C 目标路径
```

> -C 解压到指定目录，注意：要解压的目录必须存在

2、bzip2

- tar 与 bzip2 命令结合可以实现打包和压缩
- tar 只负责打包文件
- 用 bzip2 压缩 tar打包后的文件，其扩展名一般为 xxx.tar.bz2

- 在 tar 命令中有一个选项 -j 可以调用 bzip2 ，从而可以方便的实现压缩和解压缩的功能
- 命令格式如下

```
# 压缩文件
tar -jcvf 打包文件.tar.bz2 被压缩的文件/路径

# 解压缩文件
tar -jxvf 打包文件.tar.bz2
```

4、软件安装

4.1 通过 apt 安装/卸载软件

- apt 是 Advanced packaging Tool，是 Linux 下的一款 安装包管理工具
- 可以在终端中方便的安装/卸载/更新软件包

```
# 安装软件
$ sudo apt install 安装包

# 卸载软件
$ sudo apt remove 软件包

# 更新已安装的包
$ sudo apt upgrade
```

4.2 配置软件源

### 3、tail

```
// 查看文件内容
tail [option] [file]
-f 循环读取
-q 不显示处理信息
-v 显示详细处理信息
-c <number> 显示字节数
-n <number> 显示最后 number 行内容
--pid=PID 与 -f 合用，表示在进程ID，PID死掉之后结束
-q, --quiet, --silent 从不输出给文件名的首部
-s, --sleep-interval=S 与 -f 合用，表示在每次反复的间隔休眠 S 秒
```

### 4、curl

1. 访问网址

```shell
curl https://www.baidu.com
```

2. 

### 5、cut

剪切文件，并输出到标准输出

>-b  以字节为单位分割
>
>-c  以字符为单位进行分割
>
>-d  自定义分割符，默认为制表符
>
>-f  与 -d 一起使用，指定显示哪个区域
>
>-n  取消分割多字节字符。仅与 -b 标志一起使用。
>
>

### 6、 <code>tldr </code>

tldr 是一个帮助文档命令，Linux 自带帮助文档以便于你使用Linux命令，你可以使用 <code>man command</code> 或 <code>command --help</code> 查看这些文档（按 q 退出），但是这些文档过于繁琐，不利于阅读。tldr 文档简单明了，让你很容易上手这些命令。

tldr 意思就是 Too Long Don't Read，可以方便记忆。

使用方法

```shell
tldr command
```

### 7、 <code>ripgrep/rg</code>

rg 是一款强大的文本搜索工具，支持正则表达式。他是按行查找的。

(N.B. Various snaps for ripgrep on Ubuntu are also available, but none of them seem to work right and generate a number of very strange bug reports that I don't know how to fix and don't have the time to fix. Therefore, it is no longer a recommended installation option.)

#### 基本用法

```shell
# 在文件中查找关键词
# 不指定文件，默认是当前文件夹的所有文件
rg word fileName

# 在 README.md 中查找后面跟着 一个或多个 字母的 fast
# \w 表示单词，+ 表示一个或多个
rg "fast\w+" README.md
```

e.g.

```shell
❯ rg let demo
36:" Enable filetype plugins
37:filetype plugin on
38:filetype indent on
46:let mapleader = ","
66:let $LANG='en'
#==========================================================
❯ rg 'let\w+' demo
36:" Enable filetype plugins
37:filetype plugin on       
38:filetype indent on
```

```shell
# ( 在正则表达式中有特殊作用，需用 \( 转义
# 或者 用 -F
rg 'fn write\(' README.md
rg -F 'fn write('
```

e.g.

```shell
❯ rg 'if has\(' demo
76:if has("win16") || has("win32")
125:if has("gui_macvim")
153:if has("gui_running")
285:if has("mac") || has("macunix")
301:if has("autocmd")
#=========================================================
❯ rg -F 'if has(' demo
76:if has("win16") || has("win32")
125:if has("gui_macvim")
153:if has("gui_running")
285:if has("mac") || has("macunix")
301:if has("autocmd")
```

可以使用正则表达式搜索关键词

```shell
❯ rg 'if has??*??' demo
76:if has("win16") || has("win32")
125:if has("gui_macvim") 
153:if has("gui_running")
285:if has("mac") || has("macunix")
301:if has("autocmd")
```

### 8、awk

#### 基本用法

```shell
awk '{print}' file.txt
# 对 file.txt 中的每一行执行 print 命令

awk -f script.awk hello.txt
# 也可以使用脚本文件，只需用 -f

<script.awk>
{print}
```

#### 常用变量

<code>\$n</code> ：表示记录的字段。<code>\$0</code> 比较特殊，代表当前整行。

<code>FS</code> ：字段分割符。

<code>NF</code> ：当前记录中的字段数量。

<code>NR</code> ：当前记录的编号。相当于行号

使用 <code>-F</code> 指定分割符

```shell
awk -F "," '{print $3,$4}' file.txt
```

也可以在输出时插入字符

```shell
awk -F "," '{print "phone"$3,"address"$4}' file.txt
```

如果使用脚本文件，必须使用 <code>FS</code> 指定分隔符。

```shell
<script.awk>
BEGIN {
FS=","
}

{{print "phone"$3,"address"$4}}
```

分割符没有限制为单一字符，也可以是正则表达式。

<code>NF</code> 变量，也叫做“字段数量”变量。awk 会自动将该变量设置成当前记录中的字段数量。可以使用NF变量来只显示某些输入行

```shell
awk 'NF==3 {print $1,$2,$3}' file.txt
```

<code>NR</code> 输出指定行

```shell
awk '(NR < 10)||(NR > 100) {print}'
```

#### 高阶

使用正则表达式，输出包含指定字符串的行

```shell
awk '/[0-9]*/ {print}' file.txt
# 输出含有数字的行
```

// TODO 未完



### 9、sed

#### 9.1、本语法

```shell
# 命令格式
sed [option] 'sed command' filename

# 脚本格式
sed [option] -f 'sed script' filename
```

#### 9.2、option

```shell
-n 	# 只打印模式匹配的行
-e	# 默认选项，直接在 sed 命令行进行编辑
-f	# 使用脚本
-r	# 支持扩展表达式
-i	# 直接修改文件内容
```

#### 9.3、选定范围

```shell
# 打印第二行，会打印两遍，因为 sed 默认打印全部文本
sed '2p' filename

#  打印 1 - 3 行
sed '1,3p' filename

# 打印匹配的行
sed -n '/second/p' filename

# 打印包含两个模式的行，其中之一满足就可打印
sed -n '/data/,/last/p' filename

# 打印 从匹配second到第八行
sed -n '/second/,8p' filename

# 同理
sed -n '3,/last/p' filename

# 使用编辑命令，使用 {} 包含命令，使用 ; 分隔命令
# 
sed -n '1,4{=;p}' filename

# 使用 ! 对前面的匹配模式取反
# 不打印 1 - 2 行
sed -n '1,2!{=;p}'
```

#### 9.4、sed 的编辑命令

| 命令                    | 作用                                                         |
| ----------------------- | :----------------------------------------------------------- |
| <code>p</code>          | 打印匹配行（和-n选项一起合用）                               |
| <code>=</code>          | 显示文件行号                                                 |
| <code>a\\</code>        | 在定位行号后附加新文本信息                                   |
| <code>i\\</code>        | 在定位行号后插入新文本信息                                   |
| <code>d</code>          | 删除定位行                                                   |
| <code>c\\</code>        | 用新文本替换定位文本                                         |
| <code>w filename</code> | 写文本到一个文件，类似输出重定向 >                           |
| <code>r filename</code> | 从另一个文件中读文本，类似输入重定向 <                       |
| <code>s</code>          | 使用替换模式替换相应模式                                     |
| <code>q</code>          | 第一个模式匹配完成后退出或立即退出                           |
| <code>l</code>          | 显示与八进制ACSII代码等价的控制符                            |
| <code>{}</code>         | 在定位行执行的命令组，用分号隔开                             |
| <code>n</code>          | 从另一个文件中读文本下一行，并从下一条命令而不是第一条命令开始对其的处理 |
| <code>N</code>          | 在数据流中添加下一行以创建用于处理的多行组                   |
| <code>g</code>          | 将模式2粘贴到/pattern n/                                     |
| <code>y</code>          | 传送字符，替换单个字符                                       |

#### 9.5、查询

```shell
# 打印非 # 开头的行
sed -n '/^#/!p' filename
# 过滤掉以 # 开头的行和空行
sed -n '/^#/!{/^$/!p}' filename
# 删除以 # 开头的行和空行
sed -e '/^#/d' -e '/^$/d' filename
```

#### 9.6、添加

```shell
# 匹配含有 world 的行，在行首添加 Li
sed -n '/world/s/^/Li' filename
# 匹配含有 Linux 的行，在 Linux 前添加 Li
sed -n 's/Linux/Li $/' filename
# 在 Linux 之后添加 Li
sed -n 's/Linux/& Li' filename
# 在行位添加 Li
sed -n '/you/s/$/ Li/'
# 在匹配行前一行插入指定内容
sed -n '/are/i hello' filename # 添加 hello
sed -n '/are/i\hello' filename # 添加 hello
sed -n '/are/i/hello' filename # 添加 /hello
# 在匹配行的下一行插入指定内容
# 可以使用转义字符 \n 添加多行
sed -n '/are/a hello' filename # 添加 hello
sed -n '/are/a\hello' filename # 添加 hello
sed -n '/are/a/hello' filename # 添加 /hello
# TODO 未完
```







