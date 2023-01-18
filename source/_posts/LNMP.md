---
title: 手动部署LNMP
banner_img: https://i.imgtg.com/2022/12/16/H5TPD.png
date: 2022-12-16 02:41:00
tags: [学习]
---
# 前言
## LNMP的概念 :
LNMP是指一组通常一起使用来运行动态网站或者服务器的自由软件名称首字母缩写。L指Linux，N指Nginx，M一般指MySQL，P一般指PHP。
## 环境 :
*Ubuntu 20*  
使用apt来快速安装，**实际生产环境下建议编译安装**
## 安装:  
1. Nginx  
```sh
sudo apt -y install nginx
```
2. MySQL  
```sh
sudo apt -y install mysql-server
```
3. PHP  
```sh
sudo apt -y install php-fpm
``` 
## 配置MySQL:  


运行以下命令，进入MySQL。
```sh
sudo mysql
```
运行以下命令，设置root用户密码。
```sh
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password by 'mynewpassword';
```
本示例中密码以Mysql@1234为例，示例命令：
```sh
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password by 'Mysql@1234';
```
运行以下命令，退出MySQL数据库。
```sh
exit;
```
运行以下命令，对MySQL进行安全性配置。
```sh
sudo mysql_secure_installation
```
根据命令行提示，完成配置。
输入root用户的密码
设置新密码一直点y

# 配置Nginx解析PHP:  
打开Nginx默认配置文件  
```sh
vim /etc/nginx/sites-enabled/default  
```
按i进入编辑模式，修改Nginx配置文件。  
在server{}内，找到index开头的配置行，在该行中添加index.php  
在server{}内找到location ~ .php$ {}，去除以下配置行的注释符号。

```sh
location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
}
```

在Nginx网站根目录中，新建phpinfo.php文件  
```sh
<?php echo phpinfo(); ?>
```

浏览器的地址栏输入http://<IP地址>/phpinfo.php进行访问，若看到界面则成功。
