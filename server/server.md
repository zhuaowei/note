

#### 基础

1. 创建用户

```bash
adduser zhu521
```

2. 设置密码

```bash
passwd zhu521
```

3. 授权

    1. 查看 sudoers 位置

        ```bash
        whereis sudoers
        ```

    2. 增减写权限

        ```bash
        chmod -v u+w /etc/sudoers
        ```

    3. 添加用户

        ```
        zhu521	ALL=(ALL)	ALL
        ```

    4. 收回权限

        ```bash
        chmod -v u-w /etc/sudoers
        ```

4. 切换为创建的用户

    ```bash
    su zhu521
    # 第一次切换会有提示
    ```



#### SHELL

#### 安装git

```
sudo yum -y install git
```

#### 安装zsh

1. 安装

```bash
sudo yum update && sudo yum -y install zsh
```

2. 切换默认shell

```bash
# 查看所有shell路径
sudo chsh -l
#切换root shell
# sudo chsh -s /bin/zsh
切换 用户 shell
chsh -s /bin/zsh

# 启动zsh
zsh
```

> 安装完成后第一次启动zsh会有提示，选择 0，只创建.zshrc

#### 安装ohmyzsh

1. 克隆项目

```bash
git clone https://gitee.com/mirrors/oh-my-zsh.git
```

2. 安装

```bash
cd ~/.ohmyzsh/tools
./install.sh
```

> 安装后会在~/生成.ohmyzsh,可以将下载的删除了
>
> 安装完成后会提示切换默认shell，确认后选择只创建默认配置.zshrc，根据需要修改，不懂不要改

在之后说明

#### 安装插件

##### zsh-autosuggestions

1. 下载

```bash
git clone https://gitee.com/yantaozhao/zsh-autosuggestions.git
```

2. 移动到指定目录

```bash
mv ~/zsh-autosuggestions ~/.ohmyzsh/plugins/
```

##### zsh-syntax-highlighting

1. 下载

```bash
git clone https://gitee.com/zhongyuehui/zsh-syntax-highlighting.git
```

2. 移动

```bash
mv ~/zsh-syntax-highlighting ~/.ohmyzsh/plugins/
```

##### autojump

1. 下载

```bash
git clone git://github.com/wting/autojump.git
```

2. 安装

```bash
cd autojump
./install.py
```

3. 根据安装完成提示，将最后的几行添加到 .zshrc 末尾。

##### 添加 .zshrc 配置

1. 安装完上述三个插件后都要添加配置，否则不起作用。

找到 .zshrc 中的 plugins添加以下内容

```txt
plugin = (
	zsh-autosuggestions
	zsh-syntax-highlighting
	autojump
	# 根据需要添加以下插件
	# 这些是自带的，无需下载
	python
	brew
)
```

#### 配置 .vimrc

```tex
set number
```

#### 安装jdk

1. 下载jdk

> https://www.oracle.com/java/technologies/javase-downloads.html

2. 解压到指定文件夹

```bash
sudo tar -zxvf /software/jdk... -C /opt/java
```

3. 配置环境变量

```bash
sudo vim /etc/profile
```

```tex
# JAVA_HOME
export JAVA_HOME=/opt/java/jdk1.8
export JRE_HOME=$JAVA_HOME/jre
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib
```

4. 刷新

```bash
. /etc/profile
# 验证
java -version
javac -version
```

#### 安装tomcat

1. 下载

> https://tomcat.apache.org/download-80.cgi

选择 core 中的.tar.gz文件

2. 解压

```bash
sudo tar -zxvf /software/apache-... -C /opt/tomcat/
```

3. 配置环境

```bash
sudo vim /etc/profile
```

```tex
# TOMCAT_HOME
export TOMCAT_HOME=/opt/tomcat/tomcat8
```

4. 创建用户启动tomcat

> root 启动tomcat 会有危险

```bash
sudo useradd -M -s /sbin/nologin tomcat
# -M 不创建home dir
# -s 用户使用的shell
# /sbin/nologin 不允许登录
```

5. 修改权限

```bash
sudo chown -R tomcat:tomcat /opt/tomcat/
```

6. 设置service

```bash
sudo vim /etc/systemd/system/tomcat8.service
```

```tex
# Systemd unit file for tomcat
 [Unit]
 Description=Apache Tomcat Web Application Container
 After=syslog.target network.target

[Service]
 Type=forking

Environment=JAVA_HOME=/usr/java/java
 Environment=CATALINA_PID=/usr/tomcat/tomcat/temp/tomcat.pid
 Environment=CATALINA_HOME=/usr/tomcat/tomcat
 Environment=CATALINA_BASE=/usr/tomcat/tomcat
 Environment='CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC'
 Environment='JAVA_OPTS=-Djdk.tls.client.protocols="TLSv1.2" -Dsun.security.ssl.allowUnsafeRenegotiation=false -Dhttps.protocols="TLSv1.2"'
 ExecStart=/usr/tomcat/tomcat/bin/startup.sh
 ExecStop=/bin/kill -15 $MAINPID

User=tomcat
 Group=tomcat

[Install]
 WantedBy=multi-user.target
```

> 重要 修改各环境的路径

7. 建立 tomcat java 连接

```bash
cd /opt/java
sudo ln -s jdk1.8 java

cd /opt/tomcat
sudo ln -s tomcat8 tomcat
```

> 如果想取消连接
>
> sudo unlink tomcat
>
> sudo unlink java

8. 修改 logs 存放位置

```bash
sudo mkdir /var/log/tomcat8
sudo rm -rf /opt/tomcat/tomcat8/logs
cd /opt/tomcat/tomcat8
sudo ln -s /var/log/tomcat8 logs
```

> 需要修改logs的权限，否则程序没有权限创建写入文件
>
> sudo chmod a+rwx -R logs

9. 启动tomcat

```bash
# 因为添加了 tomcat8.service, 所以重新加载
sudo systemctl daemon-reload
# 启动
sudo systemctl start tomcat8

# 停止
sudo systemctl stop tomcat8
# 重启
sudo systemctl restart tomcat8

# 设置开机启动
sudo systemctl enable tomcat8
```

> 如果输入 ip:8080 没有看到tomcat欢迎页，可能是安全组没有打开8080端口或者防火墙没有开放8080端口。
>
> 如果启动失败，可能是8080端口被占用
>
> lsof -i tcp:8080 # 查看 8080 端口的程序
>
> kill -9 pid # 杀死程序

#### 安装MySQL

CentOS-7默认没有mysql的yum源，需要添加mysql源到yum

```bash
# 查看是否有mysql源
sudo yum --disablerepo=\* --enablerepo='mysql*-community*' list available
```

> 按照官方文档添加的源非常慢，这里使用清华的镜像

```bash
# 创建文件，添加镜像连接
sudo vim /etc/yum.repos.d/mysql-community.repo
```

> 根据版本选择 下面是centos7.8的
>
> https://mirrors.cnnic.cn/help/mysql/

```tex
[mysql-connectors-community]
name=MySQL Connectors Community
baseurl=https://mirrors.tuna.tsinghua.edu.cn/mysql/yum/mysql-connectors-community-el7-$basearch/
enabled=1
gpgcheck=1
gpgkey=https://repo.mysql.com/RPM-GPG-KEY-mysql

[mysql-tools-community]
name=MySQL Tools Community
baseurl=https://mirrors.tuna.tsinghua.edu.cn/mysql/yum/mysql-tools-community-el7-$basearch/
enabled=1
gpgcheck=1
gpgkey=https://repo.mysql.com/RPM-GPG-KEY-mysql

[mysql-5.6-community]
name=MySQL 5.6 Community Server
baseurl=https://mirrors.tuna.tsinghua.edu.cn/mysql/yum/mysql-5.6-community-el7-$basearch/
enabled=0
gpgcheck=1
gpgkey=https://repo.mysql.com/RPM-GPG-KEY-mysql

[mysql-5.7-community]
name=MySQL 5.7 Community Server
baseurl=https://mirrors.tuna.tsinghua.edu.cn/mysql/yum/mysql-5.7-community-el7-$basearch/
enabled=1#1为启用
gpgcheck=1
gpgkey=https://repo.mysql.com/RPM-GPG-KEY-mysql

[mysql-8.0-community]
name=MySQL 8.0 Community Server
baseurl=https://mirrors.tuna.tsinghua.edu.cn/mysql/yum/mysql-8.0-community-el7-$basearch/
enabled=1#若安装5.7，将此改为0
gpgcheck=1
gpgkey=https://repo.mysql.com/RPM-GPG-KEY-mysql
```

```
# 安装
sudo yum -y install mysql-community-server

# 查看密码
grep "password" /var/log/mysqld.log

# 进入mysql
mysql -uroot -p

# 必须修改密码太可以用
ALTER USER 'root'@'localhost' IDENTIFIED BY 'new password';

# 新建用户，开放远程登录
```

#### 安装 tmux

```bash
sudo yum -y install yum

vim ~/.zshrc
```

安装完成后需要在 <code>~/.zshrc</code> 中添加插件

```tex
plugins = (
	tmux
	python
	...
)
```

#### 安装vim插件

1. Vundle

下载Vundle包

```bash
git clone https://github.com/VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim
```

把以下内容写到在 ~/.vimrc 开头

```tex
set nocompatible              " be iMproved, required
filetype off                  " required

" set the runtime path to include Vundle and initialize
" 需要修改 Vundle 的路径"
set rtp+=~/.vim/bundle/Vundle.vim
call vundle#begin()
" alternatively, pass a path where Vundle should install plugins
"call vundle#begin('~/some/path/here')

" let Vundle manage Vundle, required
Plugin 'VundleVim/Vundle.vim'

" The following are examples of different formats supported.
" Keep Plugin commands between vundle#begin/end.
" 在这里添加其他插件

" All of your Plugins must be added before the following line
call vundle#end()            " required
filetype plugin indent on    " required
" To ignore plugin indent changes, instead use:
"filetype plugin on
"
" Brief help
" :PluginList       - lists configured plugins
" :PluginInstall    - installs plugins; append `!` to update or just :PluginUpdate
" :PluginSearch foo - searches for foo; append `!` to refresh local cache
" :PluginClean      - confirms removal of unused plugins; append `!` to auto-approve removal
"
" see :h vundle for more details or wiki for FAQ
" Put your non-Plugin stuff after this line

```

然后保存重新进入文件，执行 <code>:PluginInstall</code>

#### 免密登录

1. 本地生成公私钥

```bash
ssh-keygen
```

2. 将公钥发送到远程服务器

```bash
ssh-copy-id  -i ~/.ssh/id_rsa.pub <user>@<ip>
```

3. 配置别名

```bash
vim ~/.ssh/config
```

添加以下内容

```tex
Host <name>
	HostName <ip>
     User <user>
     Port <port>
     IdentityFile ~/.ssh/id_rsa
```

然后就可以使用 <code>ssh <name></code> 登录远程服务器。

#### 修改主机名

```bash
sudo hostname <name>
```

