# nginx

## 1、概述

### 1.1 引言

```
		  /-----># 服务器1  ----\
		/						 \
# 客户端 --------># 服务器2 -------># 数据库
				..........
```

客户端要如何处理任务，发送给谁

动态静态资源请求，节约资源

```
				  /-----># 服务器1  ----\
				/						 \
# 客户端 --> #nginx------># 服务器2 -------># 数据库
				\	    ..........
```

### 1.2 介绍

特点：

1. 稳定，7 x 24 小时
2. 简单丰富配置
3. 占用内存小，并发能力强

## 2、安装

```bash
sudo apt update
sudo apt install nginx
# 启动 nginx
sudo service nginx start
```

浏览器法访问公网IP即可看到nginx欢迎页

> http://localhost

简单配置

/etc/nginx/site-enable/default

```yml
version: '3.1'
services:
	nginx:
		restart: always
		image: daocloud.io/library/nginx:1.13.0-alpine
		contaiker: nginx
		ports:
		- 80:80
```

### 2.1 配置

核心配置

> /etc/nginx/nginx.conf

```yml
user www-data;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;
# 以上为全局配置
#--------------------------------
# worker_connections 越大， nginx并发能力越强
events {
        worker_connections 768;
        # multi_accept on;
}
#-------------------------------------
# http块
# include 引入一个外部文件
# mime.type 放着大量的媒体类型
# 
http {

        ##
        # Basic Settings
        ##

        sendfile on;
        tcp_nopush on;
        tcp_nodelay on;
        keepalive_timeout 65;
        types_hash_max_size 2048;
        # server_tokens off;

        # server_names_hash_bucket_size 64;
        # server_name_in_redirect off;

        include /etc/nginx/mime.types;
        default_type application/octet-stream;

        ##
        # SSL Settings
        ##

        ssl_protocols TLSv1 TLSv1.1 TLSv1.2; # Dropping SSLv3, ref: POODLE
        ssl_prefer_server_ciphers on;

        ##
        # Logging Settings
        ##

        access_log /var/log/nginx/access.log;
        error_log /var/log/nginx/error.log;

        ##
        # Gzip Settings
        ##

        gzip on;

        # gzip_vary on;
        # gzip_proxied any;
        # gzip_comp_level 6;
        # gzip_buffers 16 8k;
        # gzip_http_version 1.1;
        # gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

        ##
        # Virtual Host Configs
        ##

# include /etc/nginx/conf.d/*.conf;引入目录下所有的 .conf 文件
# 重要
        include /etc/nginx/conf.d/*.conf;
        include /etc/nginx/sites-enabled/*;
}

```

> /etc/nginx/conf.d/default.conf

```yml
# Default server configuration
#
server {
# server 块
# listen 监听的端口
# localhost nginx接收请求的IP
        listen 80 default_server;
        listen [::]:80 default_server;


# the path of display file
        root /var/www/html;

        # Add index.php to the list if you are using PHP
        index index.html index.htm index.nginx-debian.html;

        server_name _;

        location / {
        # location 块
        # 根据接收的请求在 root 路径查找静态资源
        # index 默认在路径下寻找 index.html/.htm
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                try_files $uri $uri/ =404;
        }
}
```

小结：

五个块

1. 全局块 
2. event块 针对并发能力配置
3. http块
4. server块 指定域名和端口
5. location块 发送到哪

主要修改server块，而server块在 .conf 文件中，因此主要修改 .conf 文件

修改 docker-compose

```yml
version: '3.1'
services:
	nginx:
		restart: always
		image: daocloud.io/library/nginx:1.13.0-alpine
		contaiker: nginx
		ports:
		- 80:80
		# 映射
		volumes:
			- /opt/docker_nginx/conf.d:/etc/nginx/conf.d
```

> /etc/docker_nginx/conf.d/default.conf

```yml
server{
	listen 80;
	server_name localhost;
	
	location / {
		root /usr/share/nginx/html;
		index index.html index.htm;
	}
}
```



## 三  反向代理

### 3.1正向代理和反向代理

正向代理

```

# 客户端 -------------x-----------------> # google
		\							   /
		  \-------># 正向代理服务器----/
```

> 正向代理
>
> 1. 正向代理服务器由客户端设立
> 2. 客户端了解代理服务器和目标服务器
> 3. 帮助实现突破访问权限，对目标服务器隐藏客户端的IP

反向代理

```
		www.taobao.com -> ip			# 淘宝
		/				   |		 /
# 客户端				# 反向代理服务器  .......> # 淘宝
									\
									  # 淘宝
```

> 反向代理：
>
> 1. 反向代理配置在服务端，与客户端无关
> 2. 客户端不知道访问的是哪台服务器
> 3. 达到负载均衡效果，隐藏服务端IP

### 3.2 反向代理

> 准备一个服务器
>
> 安装 Tomcat

```yml
server{
	listen 80;
	server_name localhsot;
	# 基于nginx反向代理 访问到 Tomcat 服务器
    location / {
    	proxy_pass http://192.168.1.1:8080/;
    }
}
# nginx 是 80 端口，Tomcat 是8080，
```

### 3.3 关于 nginx的location路径映射

```yml
# 1. = 匹配
location = / {
	# 精准扶贫
}
```

```yml
# 2. 通用匹配
location /xxx {
	# 匹配所有以 /xxx 开头的路径
}
```

```yml
# 3. 正则匹配 
location ~ /xxx {
	# 匹配所有以 /xxx 开头的路径
	# 优先级比通用高
}
```

```yml
# 4. 匹配开头路径
location ^~ /image {
	# 匹配所有以 /image 开头的路径
	# 优先级更高
}
```

```yml
# 5. 匹配结尾路径
location ~# \.(gif|jpg|png) {
	# 匹配以 .gif 或 .jpg 或 .png 结尾的路径
}
```

优先级关系

由高到低

```
(location = )
(location /xxx/yyy/zzz )
(location ^~ )
(location ~,~* )
(location /起始路径 )
(location / ) # 匹配所有路径
```

建议把优先级高的放在上面

## 4、负载均衡

```
									  # 服务器1
		10 次请求
# 客户端 -------->  # nginx			# 服务器2
```

默认三种负载均衡策略

1 轮询：平均分配，类似发牌

2 权重：能力越大责任越大

3 ip-hash：发起请求的客户端的IP不同，始终将请求发给指定的服务器

---------------------------------接下来要多台服务器-------------------------------------------

### 4.1 轮询

```yml
# 轮询的目标
# 写在前面，不要用下划线
upstream my-server {
	server 00.00.00.00:8080;
	server 00.00.00.00:8081;
}
server {
	listen 80;
	server_name localhost;
	
	location / {
	# 将请求发送给轮询的配置
		proxy_pass http://my-server;
	}
}
```

### 4.2 权重

```json
# 比轮询多点东西
upstream my-server {
	server ip:port weight=10;	# 多一句 weight
	server 00.00.00.00:8081 weight=5;
}
server {
	listen 80;
	server_name localhost;
	
	location / {
	# 将请求发送给轮询的配置
		proxy_pass http://my-server;
	}
}
```

### 4.3 ip_hash

```json
# 比 轮询多一句
upstream my-server {
    ip_hash;	# <==
	server 00.00.00.00:8080; # 这里写不写 weight 无所谓
	server 00.00.00.00:8081;
}
server {
	listen 80;
	server_name localhost;
	
	location / {
	# 将请求发送给轮询的配置
		proxy_pass http://my-server;
	}
}
```

## 4、动静分离

> nginx 并发能力公式:
>
> work_process * work_connections / (4 | 2) = nginx 最终并发能力
>
> 因为
>
> 动态资源需要 / 4
>
> 静态资源需要 / 2

### 5.1 动态资源代理

```json
location / {
    proxy_pass path;
}
```

### 5.2 静态资源代理

```json
location / {
    root staticSourcePath;
    index;
    autoindex on; # 展示所有资源，以列表形式展开。
}

# 修改docker,添加一个诗句卷，映射到nginx服务器的一个目录
# 修改配置文件
```

## 6、nginx集群搭建

### 6.1  单点故障

如果nginx服务器出现故障，则无法将请求发送到服务器。

解决办法：搭建集群，就算有挂掉的，还是能够正常工作（只要不全挂）。

需要在每台nginx服务器上安装keepalived，检测nginx是否正常工作。

客户端将请求发送给haproxy虚拟端口，有keepalived决定发送给哪台nginx服务器。

### 6.2 搭建nginx集群。

 