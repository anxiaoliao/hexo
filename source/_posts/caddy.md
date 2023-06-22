---
title: Caddy学习笔记
banner_img: 'https://i.imgtg.com/2023/06/18/OTHRdL.jpg'
abbrlink: 60933
date: 2023-06-18 22:08:08
tags:
---
# 前言
nginx的配置文件复杂，证书申请麻烦所以转向caddy

Caddy的教程较少，文档翻译的比较奇怪，所以记录下我的使用配置过程和一些问题

# 为什么使用Caddy？
1. Caddy 是一个多功能的 HTTP web服务器，并且使用Let's Encrypt提供的免费证书，自动让网站升级到HTTPS

2. Caddy 使用 Go 语言编写，编译好的二进制文件能够运行在任何支持Go语言的平台，不需要自己安装任何库。
3. Caddy 的配置简单，能够快速上手


# 安装
## [官网安装文档](https://caddyserver.com/docs/install)
***
### Debian下的安装
```
apt install -y debian-keyring debian-archive-keyring apt-transport-https
```
```
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/gpg.key' | gpg --dearmor -o /usr/share/keyrings/caddy-stable-archive-keyring.gpg
```
```
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/debian.deb.txt' | tee /etc/apt/sources.list.d/caddy-stable.list
```
```
apt update
```
```
apt install caddy
```
安装caddy成功后，输入caddy即可验证是否安装成功。
# 命令行的使用
## 托管网站
假设你的网站存放在 /home/www 
```
caddy file-server --root /home/www
```
file-server 表示启动一个文件服务， root 表示网站文件的根目录
## 使用域名
```
caddy file-server --root /home/www --domain xxx.com
```
Caddy会自动申请证书
## 反向代理
假设后端地址为8000
```
caddy reverse-proxy --to 127.0.0.1:8000
```
默认为80端口，如果需要换个端口访问
```
caddy reverse-proxy --from :2000 --to 127.0.0.1:8000
```

****
## 常用命令
1. caddy run 会运行一个前台caddy进程
2. caddy start 会启动一个在后台运行的caddy，停止使用caddy stop 命令
3. caddy reload 命令和nginx的reload命令一样

## file-server命令的具体参数

```
  -access-log
        启用访问日志
  -browse
        启用目录浏览
  -domain string
        指定一个域名
  -listen string
        监听的地址，http默认是:80,https默认是:443
  -root string
        要托管的文件服务的根目录
  -templates
        启用模板渲染
```
***
# Caddyfile的配置
1. 静态文件

```
www.xxx.com {
  root * /home/www
  file_server 
}
```
和直接使用命令一样
nginx一样一个文件内可以配置多个网站
```
www.xxx.com {
  root * /home/www
  file_server 
}
xxx.com {
  root * /home/www/xxxx
  file_server 
}
```
***
***注意*** 

修改配置文件后需要运行以下命令格式化Caddyfile文件并且把结果写入，否则可能报错
```
caddy fmt --overwrite ./Caddyfile
```

