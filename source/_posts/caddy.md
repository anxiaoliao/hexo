---
title: caddy学习笔记
banner_img: 'https://i.imgtg.com/2023/06/18/OTHRdL.jpg'
abbrlink: 60933
date: 2023-06-18 22:08:08
tags:
---
# 前言
nginx的配置文件复杂，证书申请麻烦所以转向caddy

caddy的教程较少，文档翻译的比较奇怪，所以记录下我的使用配置过程和一些问题

# Caddy 是什么？
Caddy 是一个多功能的 HTTP web服务器，并且使用Let's Encrypt提供的免费证书，自动让网站升级到HTTPS

# 安装
## 我使用Debian，其他平台参考[官网的安装文档](https://caddyserver.com/docs/install)
***
### Debian和Ubuntu系
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
