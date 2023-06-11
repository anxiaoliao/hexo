---
title: 卸载apache2
banner_img: https://i.imgtg.com/2023/06/11/OBytIq.jpg
date: 2023-01-29 20:31:43
tags: [学习]
---

# 终止apache运行
```sh
sudo service apache2 stop
```

# 删除apache
```sh
sudo apt-get --purge remove apache2
sudo apt-get --purge remove apache2.2-common
sudo apt-get autoremove
```

# 找到没有删除掉的配置文件，一并删除
```sh
sudo find /etc -name "apache" -exec rm -rf {} \;
```
```sh
sudo rm -rf /var/www
```
