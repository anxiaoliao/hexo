---
title: Nginx入门
tags: [学习,Ngiinx]
banner_img: https://pluspng.com/img-png/nginx-logo-png-transparent-nginx-logo-hd-png-img-kindpng-860x1026.png
date: 2022-09-24 10:00:00
---

# Nginx

## 1、Nginx基础





### 1.1 yum安装

配置yum仓库：  

Install the prerequisites:

```sh
sudo yum install yum-utils
```



To set up the yum repository, create the file named `/etc/yum.repos.d/nginx.repo` with the following contents:

> ```sh
> [nginx-stable]
> name=nginx stable repo
> baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
> gpgcheck=1
> enabled=1
> gpgkey=https://nginx.org/keys/nginx_signing.key
> module_hotfixes=true
> 
> [nginx-mainline]
> name=nginx mainline repo
> baseurl=http://nginx.org/packages/mainline/centos/$releasever/$basearch/
> gpgcheck=1
> enabled=0
> gpgkey=https://nginx.org/keys/nginx_signing.key
> module_hotfixes=true
> ```

By default, the repository for stable nginx packages is used. If you would like to use mainline nginx packages, run the following command:

> ```sh
> sudo yum-config-manager --enable nginx-mainline
> ```

### 1.2  源码安装： 

```sh
1、# 选择想要下载的版本，直接单击右键复制地址下载：
wget http://nginx.org/download/nginx-1.16.0.tar.gz
2、# 安装依赖
yum install gcc gcc-c++ pcre pcre-devel zlib zlib-devel
openssl openssl-devel -y
3、# 解压包
tar xf nginx-1.16.0.tar.gz
cd nginx-1.16.0
4、# 预编译
预编译主要是用来检查系统环境是否满足安装软件包的条件，
并生成Makefile文件，该文件为编译、安装、升级nginx指明了相应参数。
./configure --help 可以查看预编译参数
--prefix 指定nginx编译安装的目录；
--user=*** 指定nginx的属主
--group=*** 指定nginx的属主与属组
--with-*** 指定编译某模块
--without-** 指定不编译某模块
--add-module 编译第三方模块

./configure --prefix=/usr/local/nginx

make clean : 重新预编译时，通常执行这条命令删除上次的编译文件
make build : 编译，默认参数，可省略build参数
make install : 安装
make modules : 编译模块
make upgrade : 在线升级

5、# 编译并安装
make && make install
6、# 查看版本
/usr/local/nginx/sbin/nginx -v
7、# 启动nginx
/usr/local/nginx/sbin/nginx
8、# 检查进程及端口
ps -ef|grep nginx
netstat -ntlp|grep 80
```

### 1.3 常用命令

```sh
-v 可查看nginx的版本。
-V 可查看nginx的详细信息，包括编译的参数。
`-t 可用来测试nginx的配置文件的语法错误`
-T 可用来测试nginx的配置文件语法错误，同时还可以通过重定向备份
nginx的配置文件。
-q 如果配置文件没有错误信息时，不会有任何提示，如果有错误，则提
示错误信息，与-t配合使用。
`-s 发送信号给master处理：`
stop 立刻停止nginx服务，不管请求是否处理完
quit 优雅的退出服务，处理完当前的请求退出
reopen 重新打开日志文件，原日志文件要提前备份改名。
reload 重载配置文件
-p 设置nginx家目录路径，默认是编译时的安装路径
-c 设置nginx的配置文件，默认是家目录下的配置文件
-g 设置nginx的全局变量，这个变量会覆盖配置文件中的变量。
```



 实现配置文件语法高亮 

```sh
/www/server/nginx/src/contrib

cp -a contrib/vim/* /usr/share/vim/vimfiles/
```

### 1.4  http指令块 

```shell
# include mime.types; :定义数据类型
如果用户请求lutixia.png，服务器上有lutixia.png这个文件，后缀
名是png；
根据mime.types，这个文件的数据类型应该是image/png；
将Content-Type的值设置为image/png，然后发送给客户端
#default_type :设定默认类型为二进制流，也就是当文件类型未定义时使用这种方式，
例如在没有配置PHP环境时，Nginx是不予解析的，
此时，用浏览器访问PHP文件就会出现下载窗口。
#charset utf-8; :解决中文字体乱码
#log_format :定义日志文件格式，并默认取名为main，可以自定义该名字。也可以通过添加，删除变量来自定义日志文件的格式。
#access_log :定义访问日志的存放路径，并且通过引用
#log_format所定义的main名称设置其输出格式。
#sendfile on :用于开启高效文件传输模式。
直接将数据包封装在内核缓冲区，然后返给客户，将tcp_nopush和tcp_nodelay两个指令设置为on用于防止网络阻塞；
#keepalive_timeout 65 :设置客户端连接保持活动的超时时间。
在超过这个时间之后，服务器会关闭该连接。
#keepalive_requests 100 :设置nginx在保持连接状态最多能处理的请求数
到达请求数，即使还在保持连接状态时间内，也需要重新连接。
提示：可以用netstat -ntlpa |grep 80 查看链接状态
#gzip on :开启压缩功能，减少文件传输大小，节省带宽。
gzip_min_length 1k; :最小文件压缩，1k起压。
#gzip_types text/plain text/xml; :压缩文件类型
gzip_comp_level 3; :压缩级别，默认是1。
```

#### 1.4.1 gzip

/usr/local/nginx/conf/nginx.conf

gzip配置的常用参数

gzip on|off; #是否开启gzip

gzip_buffers 32 4K| 16 8K #缓冲(压缩在内存中缓冲几块? 每块多大?)

gzip_comp_level [1-9] #推荐6 压缩级别(级别越高,压的越小,越浪费CPU计算资源)

gzip_disable #正则匹配UA 什么样的Uri不进行gzip

gzip_min_length 200 # 开始压缩的最小长度(再小就不要压缩了,意义不在)

gzip_http_version 1.0|1.1 # 开始压缩的http协议版本(可以不设置,目前几乎全是1.1协议)

gzip_proxied # 设置请求者代理服务器,该如何缓存内容

gzip_types text/plain application/xml # 对哪些类型的文件用压缩 如txt,xml,html ,css

gzip_vary on|off # 是否传输gzip压缩标志

**注意：**

**图片/mp3这样的二进制文件,不必压缩**

**因为压缩率比较小, 比如100->80字节,而且压缩也是耗费CPU资源的.**

#### 1.4.2 autoindex

```bash
    gzip  on;
    gzip_buffers 32 4K;
    gzip_comp_level 6;
    gzip_min_length 100;
    gzip_types application/javascript text/css text/xml;
    gzip_disable "MSIE [1-6]\."; # 配置禁用gzip条件，支持正则。此处表示ie6及以下不启用gzip（因为ie低版本不支持）
    gzip_vary on;
    server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location /SRE {
           alias /usr/local/nginx/SRE/;
           autoindex on;
           autoindex_format html;
           autoindex_exact_size off;
           autoindex_localtime on;
           charset utf-8,gbk;
           #root   html;
           #index  index.html index.htm;
           
	   
        }

```

```bash
    autoindex on; #开启目录浏览
    autoindex_format html; #以html风格将目录展示在浏览器中
    autoindex_exact_size off; #切换为 off 后，以可读的方式显示文件大小，单位为 KB、MB 或者 GB
    autoindex_localtime on; #以服务器的文件时间作为显示的时间
    charset utf-8,gbk; #展示中文文件名
```

#### 1.4.3 upstream

```sh
upstream cszhi.com {
    ip_hash;
    server 192.168.8.11:80;
    server 192.168.8.12:80 down;
    server 192.168.8.13:8009 max_fails=3 fail_timeout=20s;
    server 192.168.8.146:8080;
}
```

> upstream指令指定了一个负载均衡器的名称cszhi.com

- 轮询（默认）：每个请求按时间顺序逐一分配到不同的后端服务器，如果后端某台服务器宕机，故障系统被自动剔除，使用户访问不受影响；
- Weight：指定轮询权值，**Weight值越大**，分配到的访问机率越高，主要用于后端每个服务器性能不均的情况下；
- ip_hash：每个请求按访问IP的hash结果分配，这样**来自同一个IP的访客固定访问一个后端服务器**，有效解决了动态网页存在的session共享问题；
- fair：比上面两个更加智能的负载均衡算法。此种算法可以依据页面大小和加载时间长短智能地进行负载均衡，也就是**根据后端服务器的响应时间来分配请求，响应时间短的优先分配**。Nginx本身是不支持fair的，如果需要使用这种调度算法，必须下载Nginx的upstream_fair模块；
- url_hash：按访问url的hash结果来分配请求，使**每个url定向到同一个后端服务器**，可以进一步提高后端缓存服务器的效率。Nginx本身是不支持url_hash的，如果需要使用这种调度算法，安装Nginx的hash软件包。

> 通过server指令指定后端服务器的IP地址和端口，同时还可以设定每个后端服务器在负载均衡调度中的状态 

- down：表示当前的server暂时不参与负载均衡；
- backup：预留的备份机器。当其他所有的非backup机器出现故障或者忙的时候，才会请求backup机器，因此这台机器的压力最轻；
- max_fails：允许请求失败的次数，默认为1。当超过最大次数时，返回proxy_next_upstream 模块定义的错误；
- fail_timeout：在经历了max_fails次失败后，暂停服务的时间。max_fails可以和fail_timeout一起使用。

==当负载调度算法为ip_hash时，后端服务器在负载均衡调度中的状态不能是weight和backup==



### 1.5 server指令块： 

```sh
server :		用来定义虚拟主机。
listen :		设置监听端口，默认为80端口
server_name :	域名，多个域名通过逗号或者空格隔开
Charset :		设置网页的默认编码格式
access_log :	指定该虚拟主机的独立访问日志，会覆盖前面的全局配置。
index :			设置默认的索引文件
location :		定义请求匹配规则。
error_page :	定义访问错误返回的页面，凡是状态码是500 502 503 504都会返回这个页面。
```

### 1.6 location指令块： 

```shell
location ~ \.php$ :	凡是以php结尾文件，都会匹配到这条规则。
root :				php文件存放的目录
fastcgi_pass :		指定php-fpm进程管理的ip端口或者unix套接字
fastcgi_index :		指定php脚本目录下的索引文件
fastcgi_param :		指定传递给FastCGI服务器的参数
location ~ /\.ht :	凡是请求类似.ht资源，都拒绝。
```

 配置Location指令块，来决定客户端发过来的请求URI如何处理。

```sh
Syntax: location [ = | ~ | ~* | ^~ ] uri { ... }
location @name { ... }
Default: —
Context: server, location
```

 

```sh
# 修饰符
= ：精确匹配（必须全部相等）
~ ：大小写敏感（正则表达式）
~* ：忽略大小写（正则表达式），这里要注意忽略大小写的意思是请求的字符大小写都可以，
但是不会进行大小转换，请求的大小写对应的文件必须存在。
^~ ：只需匹配uri部分
@ ：内部服务跳转
```

> 优先级参考
>
> =    ^~     ~|~*  





### 1.7 热部署

```sh
1、# 升级一般是添加新的模块，或者升级版本，所以要参考以前编译的模块，如果不添加，那么以前的模块就不能使用了
[root@node3 ~]# /usr/local/nginx/sbin/nginx -V
2、# 预编译/编译/安装：
./configure --prefix=/usr/local/nginx
make && make install
3、# 直接升级：
make upgrade
# 查看相关指令
[root@cbe121551be1 nginx-1.18.0]# cat /usr/local/src/nginx-1.18.0/Makefile 

default:	build
clean:
	rm -rf Makefile objs
build:
	$(MAKE) -f objs/Makefile
install:
	$(MAKE) -f objs/Makefile install
modules:
	$(MAKE) -f objs/Makefile modules
upgrade:
	/usr/local/nginx/sbin/nginx -t

	kill -USR2 `cat /usr/local/nginx/logs/nginx.pid`
	sleep 1
	test -f /usr/local/nginx/logs/nginx.pid.oldbin

	kill -QUIT `cat /usr/local/nginx/logs/nginx.pid.oldbin`

```

### 1.8  rewrite

rewrite是实现URL重写的关键指令，根据regex（正则表达式）部分内容， 重定向到replacement，结尾是flag标记。

```SH
rewrite <regex> <replacement> [flag];
		  正则 	  替代内容     flag标记
正则：		perl兼容正则表达式语句进行规则匹配
替代内容：  将正则匹配的内容替换成replacement
flag标记： rewrite支持的flag标记

# flag标记说明：
last #本条规则匹配完成终止当前location的规则，继续向下匹配新的location URI规则
break #本条规则匹配完成即终止，不再匹配后面的任何规则
redirect #返回302临时重定向，浏览器地址会显示跳转后的URL地址，关闭服务，无法重定向。
permanent #返回301永久重定向，浏览器地址栏会显示跳转后的URL地址，
关闭服务，依然可以重定向，清除缓存失效。
```

#### 1.8.1  实现域名跳转 

```sh
方案一：
server {
	listen 80;
	server_name lutixia.com;
	rewrite ^/(.*)$ http://www.lutixia.com/$1 permanent;
}
server {
	listen 80;
	server_name www.lutixia.com;
	location / {
		root /data/www/;
		index index.html index.htm;
	}
}
方案二：
server {
	listen 80;
	server_name www.lutixia.com lutixia.com;
	if ( $host != 'www.lutixia.com' ) {
		rewrite ^/(.*)$ http://www.lutixia.com/$1 permanent;
	}
	location / {
		root /data/www/;
		index index.html index.htm;
	}
}
方案三：
server {
	listen 80;
	server_name lutixia.com;
	return 302 http://www.lutixia.com/$request_uri
	permanent;
}
server {
	listen 80;
	server_name www.lutixia.com;
	location / {
		root /data/www/;
		index index.html index.htm;
	}
}
ps：本地需要做hosts解析
192.168.75.130 www.lutixia.com lutixia.com
```

#### 1.8.2 实现不同终端跳转 

```sh
server {
	listen 80;
	server_name lutixia.com www.lutixia.com;
	root /usr/share/nginx/html/test;
	if ( $http_user_agent ~* "iphone|android") {
		rewrite ^/(.*)$	 http://m.lutixia.com/$1;
	}
	index index.html;
}
server {
	listen 80;
	server_name m.lutixia.com;
	root /data/www/m;
	index index.html;
	location / {
		default_type text/html;
		return 200 "this is iphone|android html";
	}
}
```

#### 1.8.3 实现浏览器的语言跳转 

```sh
# 根据浏览器的语言跳转到指定url:
server {
	listen 80;
	server_name lutixia.com www.lutixia.com;
	root /usr/share/nginx/html/test;
	index index.html;
	location / {
		if ( $http_accept_language ~ "^zh-CN" ) {
			rewrite ^/(.*) /zh/$1 ;
		}
		if ( $http_accept_language ~ "^en" ) {
			rewrite ^/(.*) /en/$1 ;
		}
		root html;
		index index.html index.htm;
	}
	location ^~ /zh/ {
		root html/;
		index index.html;
	}
	location ^~ /en/ {
		root html/;
		index index.html;
	}
}
mkdir -p /usr/share/nginx/html/test/zh
mkdir -p /usr/share/nginx/html/test/en
echo "this is 中文 " > /usr/share/nginx/html/test/zh/index.html
echo "this is English " > /usr/share/nginx/html/test/en/index.html
```





## 2、 常用模块 

### 2.1 access模块 

访问控制模块 ，该模块可以实现简单的防火墙功能，过滤特定的主机。这 个模块在我们编译nginx时会默认编译进nginx的二进制文件中。 

**语法:** 

```powershell
Syntax: allow address | CIDR | unix: | all;
Default: —
Context: http, server, location, limit_except

Syntax: deny address | CIDR | unix: | all;
Default: —
Context: http, server, location, limit_except
```

- allow: 允许访问的IP或者网段。 
- deny: 拒绝访问的ip或者网段。 

从语法上看，它允许配置在http指令块中，server指令块中还有locatio指 令块中，这三者的作用域有所不同。 

如果配置在http指令段中，将对所有server(虚拟主机)生效； 

配置在server指令段中，对当前虚拟主机生效； 

配置在location指令块中，对匹配到的目录生效。 

ps: 如果server指令块，location指令块没有配置限制指令，那么将会继承 http的限制指令，但是一旦配置了会覆盖http的限制指令。 或者说作用域小的配置会覆盖作用域大的配置。  

### 2.2  auth_basic模块 

用户认证模块，对访问目录进行加密，需要用户权限认证 

这个功能特性是由ngx_http_auth_basic_module提供的，默认编译nginx 时会编译好，主要有以下两个指令。  

```powershell
Syntax: auth_basic string | off;
Default: auth_basic off;
Context: http, server, location, limit_except

Syntax: auth_basic_user_file file;
Default: —
Context: http, server, location, limit_except
```

认证的配置可以在http指令块，server指令块，location指令块配置。 

- auth_basic string ：定义认证的字符串，会通过响应报文返给客户端，也可以通过这个指令关闭认证。 
- auth_basic_user_file file ：定义认证文件。  

```sh
# 对匹配目录加密：
location /img {
auth_basic "User Auth";
auth_basic_user_file /usr/local/nginx/conf/auth.passwd;
}

auth_basic # 设置名字
auth_basic_user_file # 用户认证文件放置路径

# 生成认证文件：
yum install httpd-tools -y
htpasswd -c /usr/local/nginx/conf/auth.passwd admin
输入两次密码确定。
# 重启服务后，访问验证

htpasswd(选项)(参数)
选项
-c：创建一个加密文件；
-m：默认采用MD5算法对密码进行加密；
-d：采用CRYPT算法对密码进行加密；
-p：不对密码进行进行加密，即明文密码；
-s：采用SHA算法对密码进行加密；
-b：在命令行中一并输入用户名和密码而不是根据提示输入密码；
-D：删除指定的用户。
# 添加用户名及密码：
htpasswd -b /usr/local/nginx/conf/auth.passwd lutixia lutixia666

```

### 2.3 stub_status 模块

 状态查看模块 ，该模块可以 输出nginx的基本状态信息 。 需要源码编译。

```sh
Syntax: stub_status;
Default: —
Context: server, location

location = /status {
stub_status;
allow 192.168.75.130;
deny all;
}

Active connections:当前活动状态的连接数
accepts：统计总值，已经接受的客户端请求的总数
handled：统计总值，已经处理完成的客户端请求的总数
requests：统计总值，客户端发来的总的请求数
Reading：当前状态，正在读取客户端请求报文首部的连接的连接数
Writing：当前状态，正在向客户端发送响应报文过程中的连接数
Waiting：当前状态，正在等待客户端发出请求的空闲连接数
```

### 2.4 referer 模块 

该模块可以进行防盗链设置。 盗链的含义是网站内容本身不在自己公司的服务器上，而通过技术手段， 直接调用其他公司的服务器网站数据，而向最终用户提供此内容。 

```sh
Syntax: valid_referers none | blocked | server_names |
string ...;
Default: —
Context: server, location

location ~* \.(gif|jpg|png|swf|flv)$ {
	valid_referers none blocked lutixia.net *.jfedu.net;
	root /usr/share/nginx/html;
	if ($invalid_referer) {
		return 403;
	}
}
valid_referers 表示合法的referers设置
none： 表示没有referers，直接通过浏览器或者其他工具访问。
blocked： 表示有referers，但是被代理服务器或者防火墙隐藏；
lutixia.net： 表示通过lutixia.net访问的referers；
*.jfedu.net: 表示通过*.jfedu.net访问的referers，*表示任意host主机。
```

## 3、虚拟主机

 虚拟主机的三种模式: 

- 基于多域名方式配置虚拟主机 
- 基于多端口配置虚拟主机 
- 基于多ip配置虚拟主机  

### 3.1 多域名方式 

```sh
1、# 配置hosts文件
# Windows 本地hosts文件
C:\Windows\System32\drivers\etc\hosts
# Linux 本地hosts 文件
vim /etc/hosts

2、# 创建虚拟主机配置文件
#在http指定块中添加：
include /usr/local/nginx/conf/vhost/*.conf;
#创建配置主机配置文件
mkdir -p /usr/local/nginx/conf/vhost
vim /usr/local/nginx/conf/vhost/www.jfedu.com.conf

server {
	listen 80;
	server_name www.jfedu.com;
	location / {
		root /usr/local/nginx/html/jfedu;
		index index.html index.htm;
	}
}

vim /usr/local/nginx/conf/vhost/www.jf.com.conf
server {
	listen 80;
	server_name www.jf.com;
	location / {
		root /usr/local/nginx/html/jf;
		index index.html index.htm;
	}
}
```

### 3.2  多端口方式

```sh
server {
	listen 8080;
	server_name www.jfedu.com;
	location / {
		root /usr/share/nginx/html/jfedu;
		index index.html;
	}
}
```

### 3.3 多IP方式 

```sh
cp /etc/sysconfig/network-scripts/ifcfg-ens32{,:1}
vim /etc/sysconfig/network-scripts/ifcfg-ens32:1
修改以下信息:
NAME="ens32:1"
DEVICE="ens32:1"
IPADDR=192.168.75.188
# 重启服务：
systemctl restart network

server {
	listen 192.168.75.188:80;
	server_name www.jfedu.com;
	location / {
		root /usr/share/nginx/html/jfedu;
	index index.html;
	}
}
重启nginx服务：
systemctl restart nginx
```

## 4、反向代理

### 4.1 反向代理作用 

与正向代理（正向代理主要是代理客户端的请求）相反，反向代理主要是 代理服务器返回的数据

- 1. 可以防止内部服务器被恶意攻击（内部服务器对客户端不可见）。 
- 2. 为负载均衡和动静分离提供技术支持。

```sh
Syntax: proxy_pass URL;
Default: —
Context: location, if in location, limit_except

# 代理服务器的协议，可支持http与https。地址可以指定为域名或IP地址，以及可选端口
proxy_pass http://192.168.0.188;
proxy_pass http://192.168.0.188:8080;
proxy_pass http://localhost:9000/uri/;
```

```sh
# location和proxy_pass都不带uri路径
# 代理服务器的简单配置：
location / {
	proxy_pass http://192.168.0.114;
}
# proxy_pass 转发请求给后端服务器
# 后端服务器的配置：
location / {
	echo $host;
	root html;
	index index.html index.htm;
}
# echo $host 这个主要是来看下后端接收到的Host是什么。

# proxy_pass没有设置uri路径，但是代理服务器的location 有uri，那么代理服务器将把客户端请求的地址传递给后端服务器
# 代理服务器的配置：
location /document/data/ {
	proxy_pass http://192.168.0.114;
}
# 后端服务器的配置：
location / {
	# echo $host;
	root html/uri;
	index index.html index.htm;
}

# 如果proxy_pass设置了uri路径，则需要注意，此时，proxy_pass指令所指定的uri会覆盖location的uri。
# 代理服务器的配置：
location / {
	proxy_pass http://192.168.0.114/data/;
}
# 后端服务器的配置：
location / {
	root html;
	index index.html index.htm;
}

```

### 4.2  获取远程客户端真实ip地址 

```sh
# 代理服务器配置：
location / {
	proxy_pass http://192.168.0.114;
	proxy_set_header Host $host;
	proxy_set_header X-Real-IP $remote_addr;
	proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
}
# 后端服务器配置：
log_format main '$remote_addr - $remote_user [$time_local] "$request" '
'$status $body_bytes_sent "$http_referer" '
'"$http_user_agent" "$http_x_real_ip" "$http_x_forwarded_for"';
access_log logs/access.log main;

```

### 4.3  缓存代理服务器 

浏览器上的缓存配置，一般来说是用来减少本地IO的，请求目标的内容 会存放在浏览器本地 

```sh
# 代理服务器配置：
proxy_cache_path /data/nginx/cache max_size=10g levels=1:2 keys_zone=nginx_cache:10m inactive=10m use_temp_path=off;
server {
	listen 80;
	server_name localhost;
	location / {
		root html;
		index index.html index.htm;
		proxy_pass http://192.168.0.114;
		proxy_set_header Host $host;
		proxy_set_header X-Real-IP $remote_addr;
		proxy_cache nginx_cache;
		proxy_cache_key $host$uri$is_args$args;
		proxy_cache_valid 200 304 302 1d;
	}
}
/data/nginx/cache #缓存资源存放路径

levels #设置缓存资源的递归级别，默认为levels=1:2，表示Nginx为将要缓存的资源生成的key从后依次设置两级保存。

key_zone #在共享内存中设置一块存储区域来存放缓存的key和metadata，这样nginx可以快速判断一个request是否命中或者未命中缓存，1m可以存储8000个key，10m可以存储80000个key

max_size #最大cache空间，如果不指定，会使用掉所有disk space，当达到配额后，会删除不活跃的cache文件

inactive #未被访问文件在缓存中保留时间，本配置中如果60分钟未被访问则不论状态是否为expired，缓存控制程序会删掉文件。inactive默认是10分钟。需要注意的是，inactive和expired配置项的含义是不同的，
#expired只是缓存过期，但不会被删除，inactive是删除指定时间内未被访问的缓存文件

use_temp_path #如果为off，则nginx会将缓存文件直接写入指定的cache文件中，而不是使用temp_path存储，official建议为off，避免文件在不同文件系统中不必要的拷贝proxy_cache #启用proxy cache，并指定key_zone。如果proxy_cache off表示关闭掉缓存。
```

## 5、负载均衡 

### 5.1  工作原理 

负载均衡分为四层负载均衡和七层负载均衡。 

**四层负载均衡**是工作在七层协议的第四层-传输层，主要工作是**转发**。 它在接收到客户端的流量以后通过修改数据包的地址信息（目标地址和端 口和源地址）将流量转发到应用服务器。 

**七层负载均衡**是工作在七层协议的第七层-应用层，主要工作是**代理**。 它首先会与客户端建立一条完整的连接并将应用层的请求流量解析出来， 再按照调度算法选择一个应用服务器，并与应用服务器建立另外一条连接 将请求发送过去。  

![1607148644045](Nginx.assets/1607148644045.png)

### 5.2  配置七层均衡 

前端服务器主要配置upstream和proxy_pass： 

- upstream 主要是配置均衡池和调度方法。 
- proxy_pass 主要是配置代理服务器ip或服务器组的名字。 
- proxy_set_header 主要是配置转发给后端服务器的Host和前端客户端真实 ip。  

```sh
# 代理服务器配置
# 在http指令块下配置upstream指令块
upstream web {
	server 192.168.1.5;
	server 192.168.1.7;
}
# 在location指令块配置proxy_pass
server {
	listen 80;
	server_name localhost;
	location / {
		proxy_pass http://web;
		proxy_set_header Host $host;
		proxy_set_header X-Real-IP $remote_addr;
	}
}
# proxy_set_header Host $host;
通过这个指令，把客户端请求的host，转发给后端。
# proxy_set_header X-Real-IP $remote_addr
通过这个指令，把客户端的IP转发给后端服务器，在后端服务器的日志格式中，
添加$http_x_real_ip即可获取原始客户端的IP了。
```

#### 5.2.1 最大错误连接次数 

错误的连接由proxy_next_upstream， fastcgi_next_upstream等指令决 定 

```sh
upstream web {
	server 192.168.1.5 weight=1 max_fails=3 fail_timeout=9s;
	#先将1.5这台nginx关了。
	server 192.168.1.7 weight=1;
}
server {
	listen 80;
	server_name localhost;
	location / {
		proxy_pass http://web;
		#proxy_next_upstream error http_404 http_502;
		proxy_next_upstream off;
		proxy_set_header Host $host;
		proxy_set_header X-Real-IP $remote_addr;
	}
}
# proxy_next_upstream error http_404 http_502;
通过这个指令，可以处理当后端服务返回404等报错时，直接将请求转发给其他服务器，而不是把报错信息返回客户端。
# 在这里，我们将超时时间设置为9s，最多尝试3次，
这里要注意，尝试3次，依然遵循轮询的规则，并不是一个请求，连接3次，而是轮询三次，有3次处理请求的机会。
```

#### 5.2.2 ip_hash

通过客户端ip进行hash，再通过hash值选择后端server .

```sh
upstream web {
	ip_hash;
	server 192.168.1.5 weight=1 max_fails=3 fail_timeout=9s;
	server 192.168.1.7 weight=1;
}
```

在使用负载均衡的时候会遇到会话保持的问题,常用的方法有: 

- ip hash，根据客户端的IP，将请求分配到不同的服务器上; 
- cookie，服务器给客户端下发一个cookie，具有特定cookie的请求会分 配给它的发布者。 

#### 5.2.3 url_hash 

通过请求url进行hash，再通过hash值选择后端server 

```sh
upstream nginx_web {
	hash $request_uri consistent;
	server 192.168.75.128;
	server 192.168.75.135;
}
# 第一次请求的uri分配给后端服务器后，后面相同的uri请求都是这台服务器处理
```

#### 5.2.4 根据响应时间均衡 

fair算法会根据后端节点服务器的响应时间来分配请求，时间短的优先分配 

```sh
# 下载模块：
wget https://github.com/gnosek/nginx-upstreamfair/archive/master.zip
# 解压：
unzip master.zip
# 修改源码bug：
sed -i 's/default_port/no_port/g' ngx_http_upstream_fair_module.c
# 预编译：
./configure --prefix=/usr/local/nginx --addmodule=../echo-nginx-module --with-http_stub_status_module
--add-module=../nginx-upstream-fair-master
# 编译/安装：
make && make install
# 配置：
upstream web {
	fair;
	server 192.168.1.5 weight=1 max_fails=3 fail_timeout=9s;
	server 192.168.1.7 weight=1;
}
```

#### 5.2.5 备用服务器

```sh
upstream web {
	server 192.168.1.5 weight=1 max_fails=3 fail_timeout=9s;
	server 192.168.1.7 weight=1 backup;
}
# 1.7的服务器做备用服务器，只有在1.5得服务器不能提供服务时，才会自动顶上,否则，默认是不提供服务的。
```

### 5.3 配置四层均衡 

```
前端服务器：192.168.75.130

后端服务器1：192.168.75.128
后端服务器2：192.168.75.135
```

前端服务器主要配置stream和upstream，注意该模块需要在预编译时指 定，没有被默认编译进nginx 

#### 5.3.1 配置前端服务器 

```sh
# 预编译：
./configure --prefix=/usr/local/nginx --addmodule=../echo-nginx-module --with-http_stub_status_module --with-stream
# 编译/安装：
make && make install
# 在main全局配置stream：
events {
	worker_connections 1024;
}
stream {
	upstream web {
		# 必须要指定ip加port
		server 192.168.75.128:80;
		server 192.168.75.135:80;
	}
	server {
		listen 80;
		# 连接上游服务器超时间，超过则选择另外一个服务器
		proxy_connect_timeout 3s;
		# tcp连接闲置时间，超过则关闭
		proxy_timeout 10s;
		proxy_pass web;
	}
	log_format proxy '$remote_addr $remote_port $protocol $status [$time_iso8601] ' '"$upstream_addr" "$upstream_bytes_sent" "$upstream_connect_time"' ;
	access_log /usr/local/nginx/logs/proxy.log proxy;
}
```

#### 5.3.2  端口转发 

```sh
# 前端130上配置如下：
stream {
	upstream web {
		server 192.168.75.128:22;
	}
	server {
		listen 2222;
		proxy_connect_timeout 3s;
		proxy_timeout 10s;
		proxy_pass web;
		#proxy_set_header X-Real-IP $remote_addr;
	}
	log_format proxy '$remote_addr $remote_port $protocol $status [$time_iso8601] ' '"$upstream_addr" "$upstream_bytes_sent" "$upstream_connect_time"' ;
	access_log /usr/local/nginx/logs/proxy.log proxy;
}
# 在另外一台服务器ssh连接：
[root@node5 ~]# ssh 192.168.75.130 -p 2222
root@192.168.75.130's password:
Last login: Wed Dec 18 15:50:37 2019 from 192.168.75.130
[root@node2 ~]#
[root@node2 ~]#
# 可以看到，已经登录到了128服务器上。
```

### 5.4  后端服务器数据同步 

#### 5.4.1 部署rsync 

```sh
yum install rsync -y
# 修改配置文件：
vim /etc/rsyncd.conf
# /etc/rsyncd: configuration file for rsync daemon mode
# See rsyncd.conf man page for more options.
# rsync进程管理用户：
uid = root
gid = root
# rsync服务端口，默认是873，可以省略不写
port = 873
# 安全设置，限制软连接，如果设置为no，则文件同步到远程服务器后，会在链接文件前面增加一个目录，使链接文件失效，设置为yes则表示不加目录。
use chroot = yes
# 最大链接数
max connections = 100
# 进程pid文件
pid file = /var/run/rsyncd.pid
# 排除指定目录
exclude = lost+found/
# 开启日志，默认在系统日志中记录
transfer logging = yes
# 超时时间
timeout = 900
# 忽略不可读的错误
ignore nonreadable = yes
# 不压缩指定文件
dont compress = *.gz *.tgz *.zip *.z *.Z *.rpm *.deb
*.bz2
# 允许同步的服务器
hosts allow = 192.168.75.124
# 不允许同步的服务器
hosts deny = *
# 关闭只读权限（默认只能将本机文件同步至远程服务器，关闭后可以拉取远程
服务器内容到本机）
read only = false
# 模块
[web]
	# 本机存放同步文件目录
	path = /data/web
	# 注释目录作用
	comment = nginx web data
	# 用于同步的用户（虚拟用户）
	auth users = rsync
	# 用户认证文件
	secrets file = /etc/rsync.passwd

# 创建目录：
mkdir -p /data/web
# 创建认证文件：
echo "rsync:123456" > /etc/rsync.passwd
# 设置文件权限：
chmod 600 /etc/rsync.passwd
# 启动服务：
systemctl start rsyncd
```

#### 5.4.2 部署sersync 

```sh
# 解压包：
tar xf sersync2.5.4_64bit_binary_stable_final.tar.gz
# 移到/usr/local/目录下：
mv GNU-Linux-x86/ /usr/local/sersync
# 备份配置文件：
[root@node6 src]# cd /usr/local/sersync/
[root@node6 sersync]# cp confxml.xml confxml.xml.ba
```

## 6、nginx 配置文件

![1608101373042](Nginx.assets/1608101373042.png)

main（全局设置）、server（主机设置）、upstream（负载均衡服务器设置）和 location（URL匹配特定位置的设置）。

- main块设置的指令将影响其他所有设置；
- server块的指令主要用于指定主机和端口；
- upstream指令主要用于负载均衡，设置一系列的后端服务器；
- location块用于匹配网页位置。

server继承main，location继承server，upstream既不会继承其他设置也不会被继承 

### 6.1 全局配置

```sh
user  www www;
worker_processes auto;
error_log  /www/wwwlogs/nginx_error.log  crit;
pid        /www/server/nginx/logs/nginx.pid;
worker_rlimit_nofile 51200;

events
    {
        use epoll;
        worker_connections 51200;
        multi_accept on;
    }
```

- user是个主模块指令，指定Nginx Worker进程运行用户以及用户组，默认由nobody账号运行。
- worker_processes是个主模块指令，指定了Nginx要开启的进程数。每个Nginx进程平均耗费10M~12M内存。建议指定和CPU的数量一致即可。
- error_log是个主模块指令，用来定义全局错误日志文件。日志输出级别有debug、info、notice、warn、error、crit可供选择，其中，**debug输出日志最为最详细，而crit输出日志最少**。
- pid是个主模块指令，用来指定进程pid的存储文件位置。
- worker_rlimit_nofile用于绑定worker进程和CPU， Linux内核2.4以上可用。

 **events事件指令**是设定Nginx的工作模式及连接数上限 。

### 6.2  **HTTP服务器配置** 

> 参考 1.4  http指令块

**log_format**

有很多可选的参数用于标示服务器的活动状态，默认的是：‘$remote_addr – $remote_user [$time_local] “$request” ‘ ‘$status $body_bytes_sent “$http_referer” ‘ ‘”$http_user_agent” “$http_x_forwarded_for”‘; 

| **参数**                | **说明**                                     | **示例**                                                     |
| ----------------------- | -------------------------------------------- | ------------------------------------------------------------ |
| $remote_addr            | 客户端地址                                   | 219.227.111.255                                              |
| $remote_user            | 客户端用户名称                               | —                                                            |
| $time_local             | 访问时间和时区                               | 18/Jul/2014:17:00:01 +0800                                   |
| $request                | 请求的URI和HTTP协议                          | “GET /article-10000.html HTTP/1.1”                           |
| $http_host              | 请求地址，即浏览器中你输入的地址（IP或域名） | www.ha97.com 198.98.120.87                                   |
| $status                 | HTTP请求状态                                 | 200                                                          |
| $upstream_status        | upstream状态                                 | 200                                                          |
| $body_bytes_sent        | 发送给客户端文件内容大小                     | 1547                                                         |
| $http_referer           | url跳转来源                                  | [https](http://www.ha97.com/tag/https)://www.[google](http://www.ha97.com/category/google).com/ |
| $http_user_agent        | 用户终端浏览器等信息                         | “Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 5.1; Trident/4.0; SV1; GTB7.0; .NET4.0C; |
| $ssl_protocol           | SSL协议版本                                  | TLSv1                                                        |
| $ssl_cipher             | 交换数据中的算法                             | RC4-SHA                                                      |
| $upstream_addr          | 后台upstream的地址，即真正提供服务的主机地址 | 10.36.10.80:80                                               |
| $request_time           | 整个请求的总时间                             | 0.165                                                        |
| $upstream_response_time | 请求过程中，upstream响应时间                 | 0.002                                                        |



```sh
		server_names_hash_bucket_size 512;
        client_header_buffer_size 32k;
        large_client_header_buffers 4 32k;
        client_max_body_size 50m;
```

- client_max_body_size用来设置允许客户端请求的最大的单个文件字节数；
- client_header_buffer_size用于指定来自客户端请求头的headerbuffer大小。对于大多数请求，1K的缓冲区大小已经足够，如果自定义了消息头或有更大的Cookie，可以增加缓冲区大小。这里设置为32K；
- large_client_header_buffers用来指定客户端请求中较大的消息头的缓存最大数量和大小， “4”为个数，“128K”为大小，最大缓存量为4个128K；

