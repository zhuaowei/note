# NGINX

## 一、环境搭建

### 1.1、虚拟机安装配置

#### 1.1.1、安装Virtual Box

不多说

#### 1.1.2、安装centos7

虚拟机配置

Linux系统，redhat-64位，1G内存，20G磁盘。

选择好配置后，选择启动磁盘镜像，配置网卡。

选择存储，没有盘片，选择虚拟磁盘。

本身有一个网卡，再添加一个网卡。选择仅主机和Virtual Box。

然后就可以启动虚拟机了，配置磁盘和root密码。安装成功重启

登录成功后，修改网卡配置，使机器能联网。

`/etc/sysconfig/network-scripts/` 目录下一共有三个 `ifcfg-` 开头的文件，`lo` 代表本地，另外两个分别对应两个网卡，修改他们的配置，让 `ONBOOT=yes` 。

配置完成后重启网络服务 `systemctl restart network`

#### 1.1.3、配置远程登录

使用 `ip addr` 命令查看虚拟机的 ip地址。两个网卡之后添加的那个是可以用的，所以都试一下。（一般是 192.168 开头的）

使用Xshell连接虚拟机，修改虚拟机ip为静态ip，这样就不会变ip了，否则重连很麻烦。

```
# 修改一行
BOOTPROTO=static
# 添加几行
IPADDR=192.168.56.101 # 可以和原ip不一样，那就需要重新连接一次
NETMASK=255.255.255.0
GATEWAY=192.168.56.1 # 前三个与ip一致，最后一个写1
DNS1=8.8.8.8
```

修改完成后重启网络服务 `systemctl restart network`

### 1.2、nginx安装配置

#### 1.2.1、nginx版本

四个主流的版本

1.   nginx开源版 - 最纯粹的版本，不包含扩展功能

2.   商业版 - 包含很多扩展功能

3.   openresty - nginx + lua 开发的开源版本

4.   Tengine - 淘宝使用C语言开发的版本

#### 1.2.2、nginx编译安装（开源版本）

首先下载nginx源码压缩包，解压后进入目录，调用 configure 启动编译。

```
tar -zxvf nginx...
cd nginx...
./configure
```

编译之前需要配置编译环境。

```
yum install -y gcc
yum install -y pcre pcre-devel
yum install -y zlib zlib-devel
```

执行安装时可以指定安装的目录，一般将nginx安装在 `/usr/local/nginx` 目录下

`./configure --prefix=/usr/local/nginx`

接下来执行

```
make
make install
```

#### 1.2.3、nginx命令

进入到nginx的sbin目录下（`/usr/local/nginx/sbin`）

```shell
./nginx # 启动 nginx 
./nginx -s stop # 快速停止
./nginx -s quit # 完成请求任务后再停止
./nginx -s reload # 重新加载配置，但是不停止，其实是另开一个线程，无缝切换
```

#### 1.2.4、防火墙

启动nginx后，浏览器输入虚拟机的ip查看是否启动成功。如果没有响应，应该是防火墙的原因。

##### 1、关闭防火墙

```
systemctl stop firewalld.service
```

##### 2、停用防火墙服务

```shell
systemctl disable firewalld.service
```

#### 1.2.5、配置系统服务

##### 1、创建服务

创建一个nginx服务文件，修改路径为自己的nginx安装目录

>   vi /usr/lib/systemd/system/nginx.service

```
[Unit]
Description=nginx web service
Documentation=http://nginx.org/en/docs/
After=network.target
 
[Service]
Type=forking
PIDFile=/usr/local/nginx/logs/nginx.pid
ExecStartPre=/usr/local/nginx/sbin/nginx -t -c /usr/local/nginx/conf/nginx.conf
ExecStart=/usr/local/nginx/sbin/nginx
ExecReload=/usr/local/nginx/sbin/nginx -s reload
ExecStop=/usr/local/nginx/sbin/nginx -s stop
PrivateTmp=true
 
[Install]
WantedBy=default.target
```

保存后，重新加载系统配置

```
systemctl daemon-reload
```

之后就可以使用系统服务的方式开启nginx

```
systemctl start nginx # 启动nginx
systemctl stop nginx # 停止nginx
systemctl restart nginx # 重启nginx
systemctl status nginx # 查看nginx运行状态
systemctl reload nginx # 重新加载配置文件
```

##### 2、设置开机启动

```
systemctl enable nginx
```



## 二 、基本使用

### 2.1、目录结构

第一次启动前，nginx只包含四个目录：

1.   conf： nginx的配置文件，里面的 nginx.conf 是主配置文件，会引用其他的配置文件
2.   html：存放默认的nginx静态页面
3.   logs：存放日志文件，一个access.log 记录用户的访问记录，一个error.log，记录系统错误和访问错误，一个ngnix.pid 文件，记录主程序的pid
4.   sbin：只存放了一个nginx程序。

第一次启动后会多出四个文件夹，都是以 `_temp` 结尾的。

### 2.2、运行原理

nginx启动是分为两种进程，一个是主进程，还有很多的子进程。

启动时先启动主进程Master，Master会检查配置是否合法，如果合法就会启动子进程。子进程启动后等待用户访问，根据配置的规则解析对应的请求，读取对应的文件。

### 2.3、核心配置

>   conf/nginx.conf

```
# '#' 开头的是注释部分
worker_processes  1;  # worker的数量，取决于机器的内核数，否则配置错误会导致性能下降

events { # 事件驱动模块
    worker_connections  1024; # 一个worker可以创建的连接数
}

http { # HTTP 模块
    
    # mime.types 存放着服务端向客户端发送资源时，标注资源的类型。客户端不是依靠文件后缀解释文件的，而是根据响应头。
    # mime.types 存在着文件后缀和解释方式的对应关系，包含常见文件类型的解释方式，响应时会根据文件后缀在响应头加上。
    
    include       mime.types; 
    # 默认的响应类型，如果在mime.types 中找不到对应的关系，就使用这个
    # application/octet-stream 以数据流的方式加载，并提示用户是否要下载
    default_type  application/octet-stream; 
    
    # nginx 收到请求后，会在磁盘中找到资源文件
    # 如果不开启，nginx会将资源文件先加载到自己的程序内存中，然后再发送给网络接口，之后还有一系列的步骤...
    # 如果开启，nginx会给网络接口发送一个信号，网络接口收到信号后会读取系统的资源文件，后面就一样了...
    # 开启后，减少一层数据拷贝，速度更快
    sendfile        on; 

    # 保持长连接的超时时间，暂略
    keepalive_timeout  65; 

    server { # 虚拟主机（virtual host， vhost），一个配置就是一个主机，运行在不同的端口。
        listen       80; # 主机监听的端口
        server_name  localhost; # 主机名、域名，处理匹配的请求

        # http://192.168.56.101/user/root/demo.txt
        location / { # 匹配URI，成功后进入此区块
            root   html; # 资源目录，相对目录是相对于nginx的安装目录
            index  index.html index.htm; # 目录下的默认主页
        }

        # 发生服务器内部错误时（以下代码），转到 /50x.html
        # 链接就会变成 http://192.168.56.101/50x.html
        error_page   500 502 503 504  /50x.html;
        location = /50x.html { # 匹配 /50x.html
            root   html; # 匹配成功后就会在此目录下找到上面的资源
        }

    }

}
```

### 2.4、虚拟主机和域名解析

#### 域名、DNS、ip地址的关系

#### 浏览器、Nginx与http协议

#### 虚拟主机原理

#### 修改hosts文件解析域名

>   C:\Windows\System32\drivers\etc

```
# ip  域名
192.168.56.101	learnnginx.com
```

如果域名无法访问，ip可以访问，可能是代理软件的原因，将过滤规则添加到代理即可。

#### 域名解析

可以将域名的前缀解析到主机上，例如：`xxx.learnnginx.com` ，`www.learnnginx.com`

也可以什么都不写，他会默认写上@，就可以直接将域名解析到主机。`learnnginx.com`

还可以将所有域名的前缀都解析到主机，这个时候前缀填 `*` ，这也叫域名的泛解析。

### 2.5、nginx配置

#### 2.5.1、虚拟主机配置

配置虚拟主机，监听的端口号+主机名不能重复，也就是说，可以有相同的端口，也可以有相同的域名/主机，但是不能全部相同。

然后配置对应的目录，匹配规则等。

```
server {
    listen 80;
    server_name learnnginx.com;
}
```



>   在服务器的根目录创建 `www` 目录，在该目录下，一个目录作为一个站点。

#### 2.5.2、server_name

##### 1、完整匹配

```
server {
    listen 80;
    server_name learnnginx.com;
}
```

##### 2、匹配多个

域名之间使用空格间隔。

```
server {
    listen 80;
    server_name www.learnnginx.com test.learnnginx.com;
}
```

##### 3、通配符匹配

匹配所有 `.learnnginx.com` 结尾的域名

```
server {
    listen 80;
    server_name *.learnnginx.com;
}
```

##### 4、通配符结束匹配

匹配所有 `www.learnnginx.` 开头的域名（前提是这些域名能解析到服务器上）。

```
server {
    listen 80;
    server_name www.learnnginx.*;
}
```

##### 5、正则匹配

匹配所有前缀为数字的域名

```
server {
    listen 80;
    server_name ~^[0-9]+\.learnnginx\.com$;
}
```

`~` 代表正则匹配

`^` 开头

`$` 结尾

特殊字符需要使用 `\` 转义，例如 `.` 

#### 2.5.3、DNS与HTTPDNS

DNS（Domian Name System）域名系统是将域名和IP地址相互映射的分布式服务器。当你访问一个域名时，浏览器会先广播域名，请求解析这个域名，DNS解析出IP之后返回给浏览器，浏览器再访问这个IP。

HTTPDNS是向某一个服务器发送请求，请求它把需要解析的域名解析出来，而这个服务器的IP一般是C/S架构的应用自带的，并且，这个服务器也需要存储域名与IP的映射关系。

```
http://192.168.56.101/d?host=learnnginx.com
```

DNS与HTTPDNS的不同：

DNS是使用的UDP协议，客户端和服务端都是使用的53端口。

HTTPDNS是HTTP协议。

**应用**: 

1、很多应用都提供二级域名服务，用户申请二级域名，就可以在域名之前添加东西访问到自己的主页。例如微博的个人主页，淘宝的店铺等。

服务器1将所有的二级域名通过nginx转发到服务器2，服务器2通过解析链接，从数据库中查询出真实的IP地址，返回给用户。

2、短链接：很多短链接并不是真实的网址，用户请求短链接，nginx服务器将这个链接转发给另一台服务器，让它从服务器中查询出真实链接，然后再返回给用户。这个短链接可以使用UUID实现。

#### 2.5.4、反向代理

##### 1、网关

网关（Gate Way）就是网络的入口，例如家里使用的路由器，所有的数据请求和接收都需要走路由器。如果网络带宽100M，路由器只有10M，那么最大速度也只能到10M。

##### 2、正向代理与反向代理

正向代理与反向代理其实是相同的，只是站在了不同的角度。他们的模型都是

```
客户端  <-------> Nginx代理 <-------> 服务器端
```

正向代理是将代理设置在客户端，用户要访问一个IP，但是无法直接到达，就委托代理服务器去访问。例如在内网中要访问外网就需要一个代理，国内要访问谷歌就需要科学上网。

反向代理是将代理设置在服务端，用户要访问一个网站，这个网站有很多主机，每一个都能满足客户的需求，但是需要选择一个合适的（距离近的），Nginx代理服务器就将这个请求转发给其他服务器，服务器再发给nginx服务器，再发给用户。这种模式叫作隧道式。

一般这些服务器集群是处在内网里的，通过Nginx接收请求。这些所有的服务器接收请求之前也要走一层网关。

如果所有的请求都由Nginx服务器返回，那么并发请求多的时候，Nginx会支撑不住，所以考虑到高并发的性能，不能全部转发给Nginx服务器，需要使用另一个负载均衡器。

lvs是一个负载均衡器，它比Ngnix功能简单的多，只是将返回的数据直接发送给网关，这种叫作DR模式。那么这些服务器就需要能够直接发送数据给网关，但是它并不能接收网关传来的数据。

#### 2.5.5、负载均衡

Nginx的**高可用**就是配置一个Nginx集群，通过一台Nginx服务器转发请求给多台Nginx服务器，当这些Nginx中的服务器发生故障时，只要不是全部发生故障，就能保证服务可用（前提是负责转发的Nginx服务器正常）。

在配置高可用的同时，负载均衡也就随之配置了。负载均衡就是将所有的请求分配给Nginx服务器，使所有的机器分担请求的压力。

负载均衡是基于反向代理的，也就是配置在服务端的Nginx。

##### 1、反向代理

反向代理是配置在 `location` 块里，使用 `proxy_pass` 关键字，它和 `root` 是只能存在其一的，没有了 `root` ，自然也就没有了 `index` 。

反向代理既可以代理一个IP，一个域名，还可以是一群机器。

```
location / {
    # proxy_pass http://www.baidu.com;
    # proxy_pass http://baidu.com;
    proxy_pass http://192.168.56.101;
}
```

>   1.   必须写完整协议。
>   2.   配置单个反向代理，可以使用IP，域名。
>   3.   不带 `www` ，访问时返回 302，浏览器会重定向到真实网址
>   4.   其他的浏览器地址栏不变。

##### 2、负载均衡

首先在 `proxy_pass` 配置一个IP地址组，再在server前配置这个地址组的具体内容。

```
upstream ips {
    server 192.168.56.101;
    server 192.168.56.102;
    server 192.168.56.103;
}
server {
    listen 80;
    server_name localhost;
    
    location / {
        proxy_pass http://ips;
    }
}
```

这就是一个简单的轮询策略的负载均衡，这些服务器会挨个访问。

>   1.   `ips` 是随便配置的名字。
>   2.   `upstream` 来配置server组。
>   3.   `upstream` 是和server同级的。
>   4.   server组使用`server` 开头。

##### 3、负载均衡策略

**权重**

除了轮询，还可以配置访问浏览器的权重，权重越大，访问的机会越多。

```
upstream ips {
    server 192.168.56.101 weight=8;
    server 192.168.56.102 weight=2;
    server 192.168.56.103 weight=1;
}
```

**down**

标记为 `down` 的server不参与请求转发，相当于出了故障。

```
upstream ips {
    server 192.168.56.101 weight=8 down;
    server 192.168.56.102 weight=2;
    server 192.168.56.103 weight=1;
}
```

**backup**

标记为 `backup` 的，在有其他机器可用时，不参与请求代理，只有当其他所有机器都不可用时，再使用这个。相当于备份。

```
upstream ips {
    server 192.168.56.101 weight=8;
    server 192.168.56.102 weight=2;
    server 192.168.56.103 weight=1 backup;
}
```

##### 4、不常用的策略

`ip_hash`：根据用户访问的IP计算出Hash值，选择转发。移动设备网络波动，会更换基站，不能保持会话。

`least_conn`：转发给拥有最小连接数的服务器。设置的权重没有意义了。

`url_hash`：根据用户访问的URL的Hash值进行转发。同样不能保持会话，适合固定资源转法，例如某个URL就转到特定的服务器。

`fair`：转发给响应时间最短的服务器。会导致流量倾斜，导致流量全部打到一台机器，导致崩溃。

上面这些策略都不常用，最主要的原因就是无法保持会话和流量倾斜。

**保持会话的解决办法**：

第一种就是Cookie/Session的方式，用户请求Cookie，服务端创建Session和Cookie，并将Cookie发送给用户，自己保留Session。如果Session保留在一台服务器，用户轮询到下一台时就没有了，所以使用一台机器单独存储Session（Redis服务器），所有服务器都向它要Session，这种方式也不常用了。

第二种常用的方法就是使用Token，用户请求Token，服务器将Session和Cookie的信息整合，加密生成一个Token信息，发送给用户保存，自己不留，只保留解密的方法。用户收到Token不能写，只能读，修改后服务端就无法解密了，验证失败。这种方式也不需要专门的服务器存储session，是一种无状态的会话保持方式。

#### 2.5.6、动静分离

访问一个网页时，分为动态资源和静态资源，例如，访问一个Tomcat服务，返回一个HTML页面，这些页面中又包含各种图片，CSS，JS资源，这些资源很多，会导致并发量很多，Tomcat服务器响应很吃力，就将这些静态的资源交给Nginx代理，碰到请求静态资源，直接指向对应的文件目录即可。

##### 1、动静分离配置

配置动静分离需要配置多个 `location`。

```
server {
    listen 80;
    server_name localhost;
    
    location / {
        proxy_pass http://ips;
    }
    location /css {
        root html;
        index index.html index.htm;
    }
    location /img {
        root html;
        index index.html index.htm;
    }
    location /js {
        root html;
        index index.html index.htm;
    }
}
```

配置URL的默认匹配规则，就可以根据访问的URL自动转到对应的文件夹，访问的连接会接在root目录的后面。

##### 2、正则匹配

使用正则匹配多种静态资源。

```
server {
    listen 80;
    server_name localhost;
    
    location / {
        proxy_pass http://ips;
    }
    location ~*/(css|js|img) {
        root html;
        index index.html index.htm;
    }
}
```

使用 `~` 标志使用正则，`*` 表示不区分大小写。

##### 3、URL Rewrite

对访问的有规则的URL进行修改，从而掩盖真正的URL。

```
/1.html  => index.html?page=1
```

```
server {
    listen 80;
    server_name localhost;
    
    location / {
        rewrite ~^/([0-9]+)\.html$ /index.jsp?page=$1 break;
        proxy_pass http://192.168.56.101:8080;
    }
}
```

>   1.   配置的规则是：`rewrite <reg> <uri> [flag];`
>   2.   需要和 `proxy_pass` 配置在一块。
>   3.   需要修改的部分用 `()` 括起来，在后面的 `<uri>` 中用 `$`  取出。按顺序写序号。
