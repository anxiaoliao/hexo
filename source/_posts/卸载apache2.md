---
title: 卸载apache2
banner_img: https://i.imgtg.com/2023/02/02/0hQMg.png
date: 2023-01-29 20:31:43
tags:[学习]
---

# 终止apache运行
``
sudo service apache2 stop
``

# 删除apache
``
sudo apt-get --purge remove apache2
sudo apt-get --purge remove apache2.2-common
sudo apt-get autoremove
``

# 找到没有删除掉的配置文件，一并删除
``
sudo find /etc -name "apache" -exec rm -rf {} \;
sudo rm -rf /var/www
``