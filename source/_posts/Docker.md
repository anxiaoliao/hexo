---
title: Docker入门
tags: [学习，Docker]
banner_img: https://i.imgtg.com/2022/09/22/gytPX.jpg
date: 2022-09-24 20:00:00
---

# Docker

docker的主要目标是 ***Build,Ship and Run any App,Angwhere*** ***构建，运输，处处运行***

**构建**：做一个docker镜像

**运输**：docker pull

**运行**：启动一个容器

每一个容器，他都有自己的文件系统rootfs.

Docker 技术使用 Linux 内核和内核功能（例如 *Cgroups* *和 namespaces*）来分隔进程，以便各进程相互独立运行 

## 	1、Docker安装

### 1.1 Docker的基本组成

**镜像（image）：**
docker镜像就好比是一个模板，可以通过这个模板来创建容器服务，tomcat===>run===>tomcat01容器（提供服务），
通过这个镜像可以创建多个容器（最终服务运行或者项目运行就是在容器中的）。

**容器（container）：**

docker利用容器技术，独立运行一个或者一个组应用，通过镜像来创建的。

启动、停止、删除，基本命令。

目前就可以把这个容器理解为一个简易的linux系统

**仓库（repository /rɪˈpɒzətri/）：**

仓库就是存放镜像的地方，分为公有仓库和私有仓库。



### 1.2 安装Docker

> 环境准备

Centos7

SecureCRT 8.1

> 查看环境

```shell
# 系统内核
[root@hchost ~]# uname -r
3.10.0-957.21.3.el7.x86_64

# 系统版本
[root@hchost ~]# cat /etc/os-release 
NAME="CentOS Linux"
VERSION="7 (Core)"
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="7"
PRETTY_NAME="CentOS Linux 7 (Core)"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:centos:centos:7"
HOME_URL="https://www.centos.org/"
BUG_REPORT_URL="https://bugs.centos.org/"

CENTOS_MANTISBT_PROJECT="CentOS-7"
CENTOS_MANTISBT_PROJECT_VERSION="7"
REDHAT_SUPPORT_PRODUCT="centos"
REDHAT_SUPPORT_PRODUCT_VERSION="7"
```

> 安装

帮助文档

```sh
# 1、卸载旧版本docker
 yum remove docker \
            docker-client \
            docker-client-latest \
            docker-common \
            docker-latest \
            docker-latest-logrotate \
            docker-logrotate \
            docker-engine

# 2、安装Docker的依赖库
yum install -y yum-utils device-mapper-persistent-data lvm2

# 3、设置镜像仓库
yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo # 国外的
    
# 国内阿里的    
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
# 国内腾讯的
yum-config-manager --add-repo https://mirrors.cloud.tencent.com/docker-ce/linux/centos/docker-ce.repo

# 更新软件包索引  生成缓存
yum makecache fast

# 4、安装docker相关内容  docker-ce 社区  ee 企业版
yum install docker-ce docker-ce-cli containerd.io

# 5、启动docke
systemctl start docker

# 6、查看docker是否安装成功
docker version

# 7、测试hello-world
docker run hello-world

# 8、查看下载的镜像image
docker images

# 卸载docker
sudo yum remove docker-ce docker-ce-cli containerd.io
sudo rm -rf /var/lib/docker

```

### 1.3  启动Docker服务 

```sh
启动服务命令：		systemctl start docker

停止服务命令：		systemctl stop docker

检查运行状态命令：  systemctl status docker

重启服务命令：		 systemctl restart docker

开机启动命令：		 systemctl enable docker

开机不启动命令：	systemctl disable docker

检查开机状态命令：	systemctl is-enabled docker
```



### 1.4 镜像加速

+ 配置Docker的自定义镜像仓库地址 https://cr.console.aliyun.com/cn-shanghai/instances/mirrors

```sh
mkdir -p /etc/docker
tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://a5dwl7z9.mirror.aliyuncs.com","https://mirror.ccs.tencentyun.com","https://0bb905ba3400f35a0f83c01a53c2aec0.mirror.swr.myhuaweicloud.com"]
}
EOF
systemctl daemon-reload
service docker restart
docker info



mkdir -p /etc/docker
tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://a5dwl7z9.mirror.aliyuncs.com"]
}
EOF

# 腾讯的
mkdir -p /etc/docker
tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://mirror.ccs.tencentyun.com"]
}
EOF
systemctl daemon-reload
service docker restart
docker info

# 华为云
mkdir -p /etc/docker
tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://0bb905ba3400f35a0f83c01a53c2aec0.mirror.swr.myhuaweicloud.com"]
}
EOF
systemctl daemon-reload
service docker restart
docker info


# 国内镜像源加速站点

https://registry.docker-cn.com

http://hub-mirror.c.163.com

https://3laho3y3.mirror.aliyuncs.com

http://f1361db2.m.daocloud.io

https://mirror.ccs.tencentyun.com
```

+ 重新加载服务配置文件。

```
systemctl daemon-reload
```

+ 重启Docker服务。

```
systemctl restart docker
```

### 1.5 底层原理

**Docker是怎么工作的？**

Docker是一个Client - Server 结构的系统，Docker的守护进程运行在主机上。通过Socket从客户端访问！DockerServer 接收到Docker-Client的指令，就会执行命令。





## 2、Docker的常用命令

- Docker环境信息 — `docker [info|version]`
- 容器生命周期管理 — `docker [create|exec|run|start|stop|restart|kill|rm|pause|unpause]`
- 容器操作运维 — `docker [ps|inspect|top|attach|wait|export|port|rename|stat]`
- 容器rootfs命令 — `docker [commit|cp|diff]`
- 镜像仓库 — `docker [login|pull|push|search]`
- 本地镜像管理 — `docker [build|images|rmi|tag|save|import|load]`
- 容器资源管理 — `docker [volume|network]`
- 系统日志信息 — `docker [events|history|logs]`

### 2.1 帮助命令

```shell
docker version		# 显示docker的版本信息
docker info			# 显示docker的系统信息 显示镜像和容器
docker 命令 --help   # 帮助命令
```

帮助文档： https://docs.docker.com/engine/reference/commandline/ 

### 2.2 镜像命令

> **docker images**		查看本地所有镜像

```shell
[root@hchost ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hello-world         latest              bf756fb1ae65        6 months ago        13.3kB

REPOSITORY		# 镜像的仓库源
TAG				# 镜像的标签
IMAGE ID		# 镜像的ID

Usage:  docker images [OPTIONS] [REPOSITORY[:TAG]]
Options:
  -a, --all             Show all images (default hides intermediate images)
  -q, --quiet           Only show numeric IDs
```

> **docker search**		搜索镜像

```shell
docker search [OPTIONS] TERM

--automated :只列出 automated build类型的镜像；

--no-trunc :显示完整的镜像描述；


# 搜索收藏大于3000以上的信息
[root@hchost ~]# docker search mysql --filter=STARS=3000
NAME                DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
mysql               MySQL is a widely used, open-source relation…   9755                [OK]                
mariadb             MariaDB is a community-developed fork of MyS…   3562                [OK]   
[root@hchost local]# docker search -f STARS=130 nginx


```

| **参数**        | **说明**         |
| --------------- | ---------------- |
| **NAME**        | 镜像名称         |
| **DESCRIPTION** | 镜像说明         |
| **STARS**       | 点赞数量         |
| **OFFICIAL**    | 是否是官方的     |
| **AUTOMATED**   | 是否是自动构建的 |

> **docker pull**		下载镜像

- [ ] ```shell
  # docker pull 镜像名[:tag]
  [root@hchost ~]# docker pull mysql
  Using default tag: latest			# tag: latest 默认是下载最新版
  latest: Pulling from library/mysql
  8559a31e96f4: Pull complete 		# 分层下载 docker images的核心 联合文件系统
  d51ce1c2e575: Pull complete 
  c2344adc4858: Pull complete 
  fcf3ceff18fc: Pull complete 
  16da0c38dc5b: Pull complete 
  b905d1797e97: Pull complete 
  4b50d1c6b05c: Pull complete 
  571e8a282156: Pull complete 
  e7cc823c6090: Pull complete 
  89f6364a8689: Pull complete 
  55e5b10f33a4: Pull complete 
  68129e7a0316: Pull complete 
  Digest: sha256:c455bbcaa8b9c5c636c45f6184f970caeb3d8b545a0390e1b72a253e07aef8fd	# 签名
  Status: Downloaded newer image for mysql:latest
  docker.io/library/mysql:latest		# 真实地址
  
  # 等价于
  [root@hchost ~]# docker pull mysql
  [root@hchost ~]# docker pull docker.io/library/mysql:latest	
  
  # 指定版本下载
  [root@hchost ~]# docker pull mysql:5.7
  5.7: Pulling from library/mysql
  8559a31e96f4: Already exists 		#linux核心，联合文件系统共用之前的镜像，不用重复下载
  d51ce1c2e575: Already exists 
  
  ```

> **docker rmi**  删除镜像

```shell
# 指定镜像ID删除，-f强制删除
[root@hchost ~]# docker rmi -f 5ac22cccc3ae

# 删除所有镜像
[root@hchost ~]# docker rmi -f $(docker images -aq)

```

> 导入导出镜像

```sh
# 导出  centos是REPOSITORY
[root@hchost ~]# docker image save centos > docker-centos.tar.gz
# 导入
[root@hchost ~]# docker image load -i docker-centos.tar.gz  

docker save <myimage>:<tag> | gzip > <myimage>_<tag>.tar.gz

gunzip -c <myimage>_<tag>.tar.gz | docker load
```

> 查看镜像详细信息

```sh
[root@hchost local]# docker image inspect 509ff2fb81dd
```



### 2.3 容器命令

说明：有了镜像才可以创建容器

**新建容器并启动**

```shell
docker run [可选参数] image
# 参数说明
--name="Name"		容器名字 tomcat 用来区分容器
-d					后台方式运行
-it					使用交互式方式运行，进入容器查看内容
-p					指定容器的端口 如:-p 8080:8080
		-p ip:主机端口:容器端口
		-p 主机端口:容器端口  (常用)
		-p 容器端口
		容器端口
-P					随机指定端口

# 测试 启动并进入容器
[root@hchost ~]# docker run -it centos /bin/bash
[root@87b5af922b79 /]# 
# 从容器退出主机
[root@87b5af922b79 /]# exit
exit

# 后台运行，不进交互
[root@hchost ~]# docker run -itd centos /bin/bash
```

**列出所有运行中的容器**

```shell
# docker ps 命令
		列出当前正在运行的容器
-a		列出当前正在运行的容器+顺带出历史运行过的容器
-n=?	显示最近创建的容器
-q		显示容器编号

[root@hchost ~]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
[root@hchost ~]# docker ps -a

[root@hchost ~]# docker ps -a -n=1
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
87b5af922b79        centos              "/bin/bash"         9 minutes ago       Exited (0) 7 minutes ago                       quirky_sanderson
[root@hchost ~]# docker ps -aq
87b5af922b79
163f9e63c8c8
```

**容器退出**

```shell
exit		# 容器停止并退出
Ctrl+p+q	# 容器不停止退出

[root@hchost ~]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
[root@hchost ~]# docker run -it centos /bin/bash
[root@e28a3bb96b93 /]#       # 这个地方操作了Ctrl+p+q，退回到主机，查看发现容器还在运行
[root@hchost ~]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
e28a3bb96b93        centos              "/bin/bash"         22 seconds ago      Up 21 seconds                           loving_haslett
```

**删除容器**

```shell
docker rm 容器ID					   # 删除指定容器，不能删除正在运行的容器
docker rm -f $(docker ps -aq)		# 删除所有容器
docker ps -a -q|xargs docker rm 	# 删除所有容器

[root@hchost ~]# docker rm 87b5af922b79
87b5af922b79
[root@hchost ~]# docker rm -f $(docker ps -aq)
e28a3bb96b93
54fad9a1d452
163f9e63c8c8
```

启动和停止容器

```shell
docker start 容器ID
docker restart 容器ID
docker stop  容器ID
docker kill 容器ID
```

### 2.4 常用的其他命令

#### 2.4.1 **后台启动容器**

```shell
[root@hchost ~]# docker run -d centos

# 问题 docker ps 发现centos停止了
# 常见的坑，docker容器使用后台运行，就必须要有一个前台进程，docker发现没有应用，就会自动停止
# Nginx 容器启动后，发现自己没有提供服务，就会立刻停止，就是没有程序了
```

#### **2.4.2 查看日志**

```shell
# 显示日志最新的10行
[root@hchost ~]# docker logs -t -f --tail 10 cc158905e404

# shell脚本模拟写日志信息
[root@hchost ~]# docker run -d centos /bin/bash -c "while true;do echo hc;sleep 1;done"
```

#### **2.4.3 查看容器中进程信息**  ps

```shell
# 命令 docker top 容器ID
[root@hchost ~]# docker top ee00c0e571a5
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
root                19070               19054               0                   16:08               pts/0               00:00:00            /bin/bash
```

#### **2.4.4 查看镜像的元数据**

```shell
# docker inspect 容器ID
[root@hchost ~]# docker inspect ee00c0e571a5
```



#### **2.4.5 进入当前正在运行的容器**

```shell
# 通常容器都是使用后台方式进行的，需要进入容器，修改一些配置

# 命令1
docker exec -it 容器id /bin/bash
[root@hchost ~]# docker exec -it ee00c0e571a5 /bin/bash

# exit退出后，容器还在运行
[root@ee00c0e571a5 /]# exit
exit
[root@hchost ~]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
ee00c0e571a5        centos              "/bin/bash"         About an hour ago   Up About an hour                        crazy_pasteur

# 命令2
docker attach 容器id
[root@hchost ~]# docker attach $(docker ps -q)
# exit退出后，容器退出结束

# docker exec		进入容器后开启一个新的终端，可以在里面操作（常用）
# docker attach     进入容器正在执行的终端，不会启动新的进程

```

#### **2.4.6 从容器内拷贝文件到主机上**

```shell
docker cp 容器id:容器内路径	目的的主机路径
[root@hchost ~]# docker cp 28ba1b1dbd31:/home/123.log /hc
# 即使容器没有运行，是停止的，也可以拷贝
# 未来使用 -v 卷的技术，可以实现自动拷贝
```

#### 2.4.7 提高权限

```sh
docker run -it --name hc_centos -p 8023:80 -v /data/:/data/ --privileged=true hc_centos:7.9 /usr/sbin/init

```

#### 2.4.8 查询容器IP

```sh
[root@hchost ~]# docker inspect c873644486fc|grep -ai ipaddr|tail -1|grep -oE "([0-9]{1,3}\.){3}[0-9]{1,3}"

```

#### 2.4.8 查询指定配置信息

```sh
# -i 不区分大小写   -A 指定显示行数
[root@hchost _data]# docker inspect c873644486fc3c5|grep -ai mounts -A 10
```




### 2.5 Docker命令集

<table class="table table-bordered table-striped table-condensed">
   <tr>
      <td>命令</td>
      <td>说明</td>
   </tr>
   <tr>
      <td>容器生命周期管理</td>
      <td></td>
   </tr>
   <tr>
      <td>run</td>
      <td>创建一个新容器并运行</td>
   </tr>
   <tr>
      <td>start</td>
      <td>开启一个容器并使其在后台运行</td>
   </tr>
   <tr>
      <td>stop</td>
      <td>停止一个容器</td>
   </tr>
   <tr>
      <td>restart</td>
      <td>重启一个容器</td>
   </tr>
   <tr>
      <td>kill</td>
      <td>杀掉一个容器进程</td>
   </tr>
   <tr>
      <td>rm</td>
      <td>删除容器</td>
   </tr>
   <tr>
      <td>pause</td>
      <td>暂停容器</td>
   </tr>
   <tr>
      <td>unpause</td>
      <td>恢复暂停容器</td>
   </tr>
   <tr>
      <td>create</td>
      <td>从镜像中创建一个容器</td>
   </tr>
   <tr>
      <td>exec</td>
      <td>对一个容器执行shell命令</td>
   </tr>
   <tr>
      <td>容器操作</td>
      <td></td>
   </tr>
   <tr>
      <td>ps</td>
      <td>列出容器</td>
   </tr>
   <tr>
      <td>inspect</td>
      <td>获取容器或镜像的元数据</td>
   </tr>
   <tr>
      <td>top</td>
      <td>查看正在运行中的容器进程信息</td>
   </tr>
   <tr>
      <td>attach</td>
      <td>链接正在运行的容器</td>
   </tr>
   <tr>
      <td>events</td>
      <td>从docker服务器获取事件</td>
   </tr>
   <tr>
      <td>logs</td>
      <td>获取docker日志</td>
   </tr>
   <tr>
      <td>wait</td>
      <td>让一个容器进入等待，使其进入阻塞状态</td>
   </tr>
   <tr>
      <td>export</td>
      <td>讲一个容器的文件系统打包至tar</td>
   </tr>
   <tr>
      <td>port</td>
      <td>列出一个容器的端口映射情况</td>
   </tr>
   <tr>
      <td>container</td>
      <td>管理已经运行的容器的</td>
   </tr>
   <tr>
      <td>deploy</td>
      <td>部署新的堆栈或更新已有堆栈的</td>
   </tr>
   <tr>
      <td>update</td>
      <td>更新容器</td>
   </tr>
   <tr>
      <td>rename</td>
      <td>重命名容器</td>
   </tr>
   <tr>
      <td>volume</td>
      <td>卷管理</td>
   </tr>
   <tr>
      <td>容器文件系统操作</td>
      <td></td>
   </tr>
   <tr>
      <td>commit</td>
      <td>提交一个容器的文件系统，使之生成一个新的镜像</td>
   </tr>
   <tr>
      <td>cp</td>
      <td>向一个正在运行的容器复制文件，或将容器中的文件复制出来</td>
   </tr>
   <tr>
      <td>diff</td>
      <td>检查一个容器文件系统更改情况</td>
   </tr>
   <tr>
      <td>镜像仓库操作</td>
      <td></td>
   </tr>
   <tr>
      <td>login</td>
      <td>docker登入</td>
   </tr>
   <tr>
      <td>logout</td>
      <td>docker登出</td>
   </tr>
   <tr>
      <td>pull</td>
      <td>拉取镜像</td>
   </tr>
   <tr>
      <td>push</td>
      <td>推送镜像至服务器</td>
   </tr>
   <tr>
      <td>search</td>
      <td>在docker hub上查询镜像</td>
   </tr>
   <tr>
      <td>镜像管理</td>
      <td></td>
   </tr>
   <tr>
      <td>images</td>
      <td>列出镜像</td>
   </tr>
   <tr>
      <td>rmi</td>
      <td>删除镜像</td>
   </tr>
   <tr>
      <td>tag</td>
      <td>修改本地某一镜像的标记，使其镜像属于某一仓库</td>
   </tr>
   <tr>
      <td>build</td>
      <td>通过指定Dockerfile文件编译镜像</td>
   </tr>
   <tr>
      <td>history</td>
      <td>查看镜像历史</td>
   </tr>
   <tr>
      <td>save</td>
      <td>将制定镜像保存成tar文件</td>
   </tr>
   <tr>
      <td>load</td>
      <td>从tar中恢复镜像</td>
   </tr>
   <tr>
      <td>import</td>
      <td>从tar中创建一个新镜像</td>
   </tr>
   <tr>
      <td>checkpoint</td>
      <td>设置checkpoint，类似于恢复点，可以让镜像撤销到曾经设置的某一个checkpoint上</td>
   </tr>
   <tr>
      <td>image</td>
      <td>docker镜像管理</td>
   </tr>
   <tr>
      <td>manifest</td>
      <td>docker镜像清单管理</td>
   </tr>
   <tr>
      <td>trust</td>
      <td>docker可信镜像管理</td>
   </tr>
   <tr>
      <td>集群管理</td>
      <td></td>
   </tr>
   <tr>
      <td>swarm</td>
      <td>docker集群管理工具</td>
   </tr>
   <tr>
      <td>node</td>
      <td>docker集群节点控制</td>
   </tr>
   <tr>
      <td>stack</td>
      <td>docker集群堆栈管理</td>
   </tr>
   <tr>
      <td>其他命令</td>
      <td></td>
   </tr>
   <tr>
      <td>info</td>
      <td>查询docker信息</td>
   </tr>
   <tr>
      <td>version</td>
      <td>查询docker版本</td>
   </tr>
   <tr>
      <td>system</td>
      <td>docker系统管理</td>
   </tr>
   <tr>
      <td>stats</td>
      <td>docker容器资源使用统计</td>
   </tr>
   <tr>
      <td>config</td>
      <td>管理docker配置</td>
   </tr>
   <tr>
      <td>network</td>
      <td>docker网络管理</td>
   </tr>
   <tr>
      <td>plugin</td>
      <td>docker插件管理</td>
   </tr>
   <tr>
      <td>secret</td>
      <td>docker敏感信息管理</td>
   </tr>
   <tr>
      <td>service</td>
      <td>docker服务管理</td>
   </tr>
   <tr>
      <td></td>
   </tr>
</table>

 能将EXCEL表格转化成HTML格式的在线网址：http://pressbin.com/tools/excel_to_html_table/index.html 

 Markdown之表格的处理:https://www.ituring.com.cn/article/3452 

将第一个 <table>变成<table class="table table-bordered table-striped table-condensed">



### 2.6 作业练习

> 安装Nginx

```shell
# 1、搜索镜像
[root@hchost ~]# docker search nginx
# 2、下载镜像
[root@hchost ~]# docker pull nginx
# 3、新建并运行容器  -d后台运行		-p 指定端口 公网端口：容器内端口
# 运行测试
[root@hchost ~]# docker run -d --name nginx01 -p 3344:80 nginx
[root@hchost ~]# curl localhost:3344
# 进入容器 查询nginx配置
[root@hchost ~]# docker exec -it nginx01 /bin/bash
root@bced0d015071:/# whereis nginx
nginx: /usr/sbin/nginx /usr/lib/nginx /etc/nginx /usr/share/nginx
root@bced0d015071:/# cd /etc/nginx/
root@bced0d015071:/etc/nginx# ls
conf.d  fastcgi_params  koi-utf  koi-win  mime.types  modules  nginx.conf  scgi_params  uwsgi_params  win-utf
```

端口暴露的概念



> 安装Tomcat

```shell
# 官方版本
$ docker run -it --rm tomcat:9.0

# 我们之前的启动都是后台，停止了容器之后，docker ps -a 容器还是可以查到
# docker run -it --rm  一般是用来测试，用完及删除

# 创建镜像，启动容器
[root@hchost ~]# docker run -it -p 8080:8080 tomcat:9.0
# 发现问题
# 1.linux命令少了
# 2.webapps下没有内容，阿里云镜像默认是最小的镜像，不必要的都删除了

# 2的解决方法
root@369f96c23d9e:/usr/local/tomcat# cp -r webapps.dist/* webapps
```

> 部署 es + kibana

```shell
# es 暴露的端口很多
# es 十分的耗内存
# es 的数据一般需要放置到安全目录！挂载

#--net somenetwork		网络配置
# 启动
docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:7.6.2

# 查看容器CPU、内存使用状态
docker stats
# 测试es是否安装好了
[root@hchost ~]# curl localhost:9200
#增加内存的限制，修改配置文件 -e 环境配置修改
docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -e ES_JAVA_OPTS="-Xms64m -Xmx512m" elasticsearch:7.6.2
```

### 2.7 可视化

```shell
# portainer  docker图形化界面管理工具 提供一个后台面板供我们操作
docker run -d -p 8088:9000 --restart always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
```

Rancher （CI/CD再用）

访问测试： [http://139.224.133.172:8088](http://139.224.133.172:8088/) 

username:admin

passwd:123qweasd



选择本地的


进入之后的面板


## 3、Docker镜像讲解

### 3.1 镜像是什么

镜像是一种轻量级、可执行的独立软件包，用来打包软件运行环境和基于运行环境开发的软件，它包含运行某个软件所需的所有内容，包括代码、运行时、库、环境变量和配置文件。

所有的应用，直接打包docker镜像，就可以直接跑起来。

如何得到镜像：

- 从远处仓库下载
- 朋友拷贝给你
- 自己制作一个镜像 DockerFile

### 3.2 Docker镜像加载原理

> UninoFS（联合文件系统）

我们下载的时候看到的一层层就是这个！

UninoFS（联合文件系统）：Union文件系统（UninoFS）是一种分层、轻量级并且高性能的文件系统，它支持对文件系统的修改作为一次提交来一层层的叠加，同时可以将不同目录挂载到同一个虚拟文件系统下(unite several directories into a single virtual file system)。Union文件系统是Docker镜像的基础。镜像可以通过分层来进行继承，基于基础镜像（没有父镜像），可以制作个各种具体的应用镜像。

特性：一次同时加载多个文件系统，但从外面看起来，只能看到一个文件系统，联合加载会把各层文件系统叠加起来，这样最终的文件系统会包含所有底层的文件和目录。

> Docker镜像加载原理

docker的镜像实际上由一层一层的文件系统组成，这种层级的文件系统UnionFS。

**bootfs**(boot file system)主要包含 bootloader和kernel，bootloader主要是引导加载kernel， Linux刚启动的时会加载bootfs文件系统，在Docker镜像的最底层是bootfs。这一层与我们典型的Linux/Unix系统是一样的，包含boot加载器和内核。当boot加载完成之后整个内核就都在内存中了，此时内存的使用权已有bootfs转交给内核，此时系统也会卸载bootfs。

**rootfs** (root file system) ，再bootfs之上。包含的就是典型 Linux 系统中的/dev，/proc，/bin，/etc等标准目录和文件。rootfs就是各种不同的操作系统发行版，比如Ubuntu，Centos等等。

平时我们安装进虚拟机的Centos都是好几个G，为什么docker这里才200M？

对于一个精简的OS，rootfs可以很小，只需要包含最基本的命令，工具和程序就可以了，因为底层直接用Host的kernel，自己只需要提供rootfs就可以了。由此可见对于不同的linux发行版，bootfs基本是一致的，rootfs会有差别，因此不同的发行版可以共用bootfs。

### 3.3 分层理解　　 

> 分层的镜像

我们可以去下载一个镜像，观察下载的日志输出，可以看到一层一层的在下载。

思考：为什么Docker镜像要采用这种分层的结构呢？

最大的好处，我觉得莫过于是**资源共享**了。比如有多个镜像都从相同的Base镜像构建而来，那么宿主机只需要在磁盘上保留一份base镜像，同时内存中也只需要加载一份base镜像，这样就可以为所有的容器服务了，而且镜像的每一层都可以 被共享。

查看镜像分层的方式也以通过 docker image inspect 命令。

**理解：**

所有的Docker镜像都起始于一个基础镜像层，当进行修改或增加新的内容时，就会在当前镜像层之上，创建新的镜像层。

举一个简单的例子，假如基于Ubuntu Linux 16.04 创建一个新的镜像，就是新镜像的第一层；如果在该镜像中添加Python包，就会在基础镜像层之上创建第二个镜像层；如果继续添加一个安全补丁，就会创建第三个镜像层。



在添加额外的镜像层的同时，镜像始终保持是当前所有镜像的组合，理解这一点非常重要。下图中举了一个简单的例子，每个镜像层包含3个文件，而镜像包含了来自两个镜像层的6个文件。



上图中的镜像层跟之前图中的略有区别，主要目的是便于展示文件。

下图中展示了一个稍微复杂的三层镜像，在外部看来整个镜像只有6个文件，这是因为最上层中的文件7是文件5的一个更新版本。



这种情况下，上层镜像层中的文件覆盖了底层镜像层中的文件。这样就使得文件的更新版本作为一个新镜像层添加到镜像当中。

Docker 通过存储引擎（新版本采用快照机制）的方式来实现镜像层堆栈，并保证多镜像层对外展示位统一的文件系统。

Linux 上可用的存储引擎有 AUFS、Overlay2、Device Mapper、Btrfs 以及ZFS。顾名思义，每种存储引擎都基于Linux中对应的文件系统或者块设备技术，并且每种存储引擎都有其独有的性能特点。

Docker 在Windows 上仅支持windowsfilter一种存储引擎，该引擎基于NTFS文件系统上实现了分层和CoW。

下图展示了与系统显示相同的三层镜像。所有镜像层堆叠并合并，对外提供统一的视图。



> 特点

Docker镜像都是只读的，当容器启动时，一个新的可写层别加载到镜像的顶部！

这一层就是我们通常说的容器层，容器之下的都叫镜像层。

### 3.4 commit 镜像 

```shell
docker commit 提交容器成为一个新的副本
docker commit -m="提交的描述信息" -a="作者" 容器id 目标镜像名：[TAG]

[root@hchost ~]# docker commit -m="add webapps" -a="hc" 369f96c23d9e tomcat_hc:1.0
sha256:581b5c37a65f77fdef30fccf6ab254f17a976096dfc1af5b034c9c21f5511188

[root@hchost ~]# docker images
REPOSITORY            TAG                 IMAGE ID            CREATED             SIZE
tomcat_hc             1.0                 581b5c37a65f        5 seconds ago       652MB
tomcat                9.0                 b4f6a90c69a4        3 days ago          647MB
portainer/portainer   latest              62771b0b9b09        3 days ago          79.1MB
nginx                 latest              8cf1bfb43ff5        4 days ago          132MB
elasticsearch         7.6.2               f29a1ee41030        4 months ago        791MB
centos                7.2.1511            9aec5c5fe4ba        16 months ago       195MB
```

学习方式：理解概念，但是一定要实践，最后实践和理论想结合一次搞定这个知识

```shell
如果你想保存当前容器的状态，就可以通过commit提交，获得一个镜像。
就好比我们以前学习VM的时候，快照。
```

## 4、容器数据卷

### 4.1 什么是容器数据卷

**docker的理念回顾**

将应用和环境打包成一个镜像

如果数据都在容器中，那么我们容器删除，数据就会丢失！==需求：数据可以持久化==

容器之间可以有个数据共享的技术！Docker容器中产生的数据，同步到本地！

这就是卷技术！目录的挂载，将我们容器内的目录，挂载到Linux上面。

总结：容器的持久化和同步操作！容器间也是可以数据共享的！

```sh
# 查看卷列表
[root@hchost nginx]# docker volume ls
DRIVER    VOLUME NAME

# 创建一个卷
[root@hchost nginx]# docker volume create hc_nginx

# 查看卷路径
[root@hchost nginx]# docker volume inspect hc_nginx

# 使用卷
[root@docker01 ~]# docker run -d -p 9000:80 -v clsn:/usr/share/nginx/html nginx:latest 
[root@docker01 ~]# docker run  -d  -P  --volumes-from 079786c1e297 nginx:latest

# 删除卷
# 删除未被任何容器使用的本地卷
docker volume prune [OPTIONS]

docker volume rm [OPTIONS] VOLUME [VOLUME...]
```



### 4.2 使用数据卷

> 方式一：直接使用命令来挂载 -v

```shell
docker run -it -v 主机目录:容器目录
# 测试
[root@hchost ~]# docker run -it -v /home/test:/home centos /bin/bash
# 启动起来可以通过 docker inspect 容器id  查看是否挂载成功
```



### 4.3 实战：安装Mysql

思考：Mysql的数据持久化

```shell
# 获取镜像
[root@hchost ~]# docker pull mysql:5.7

#注意：安装启动mysql是需要配置密码的 官方的命令如下（参考） -e配置环境
$ docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag

# 运行容器，做数据挂载  
-d 后台运行
-p 端口映射
-v 卷挂载
-e 环境配置
--name 容器命名
[root@hchost ~]# docker run -d -p 3310:3306 -v /home/mysql/conf:/etc/mysql/conf.d -v /home/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 --name=mysql mysql:5.7

# Navicat---连接到服务器的3310---3310和容器内的3306映射，这个时候我们就可以连接上了。
```

### 4.4 具名挂载和匿名挂载

```shell
# 匿名挂载
-v 容器内路径
# -P 随机映射端口
docker run -d -P --name nginx01 -v /etc/nginx nginx

# 查看所有的卷的情况
[root@hchost ~]# docker volume ls
DRIVER              VOLUME NAME
local               4f6867a257a8c5062f439f4705d05f35451395817c14169392ece8dd4742f318
# 这种就是匿名挂载，我们在 -v 只写了容器内的路径，没有写容器外的路径

# 具名挂载
# 通过 -v 卷名:容器内路径
[root@hchost ~]# docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx nginx
b86bf3aa54eb32f72b2eb3ff72af0b7637bba24e6ffde7eb57b502964d28cd1a
[root@hchost ~]# docker volume ls
DRIVER              VOLUME NAME
local               juming-nginx

# 查看卷的位置
[root@hchost ~]# docker volume inspect juming-nginx
[
    {
        "CreatedAt": "2020-07-31T14:57:42+08:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/juming-nginx/_data",
        "Name": "juming-nginx",
        "Options": null,
        "Scope": "local"
    }
]
# "Mountpoint": "/var/lib/docker/volumes/juming-nginx/_data"就是宿主机上此容器/etc/nginx卷映射的位置

```

所有的docker容器内的卷，没有指定目录的情况下都是在==/var/lib/docker/volumes/***/_data==下

通过具名挂载可以方便的找到我们的一个卷，大多数情况在使用的都是`具名挂载`

```shell
# 如何确定是具名挂载还是匿名挂载，还是指定路径挂载
-v 容器内路径			  # 匿名挂载
-v 卷名：容器内路径	  		# 具名挂载
-v /宿主机路径：容器内路径   #	指定路径挂载	
```

拓展：

```shell
# 通过 -v 容器内路径：ro rw 改变读写权限
ro	readonly	# 只读
rw	readwrite	# 可读可写
# 一旦设定了容器权限，容器对我们挂载出来的内容就有限定了
docker run -d -P --name nginx02 -v juming-nginx:ro nginx
docker run -d -P --name nginx02 -v juming-nginx:rw nginx

# ro 只要看到ro就说明这个路径只能通过宿主机来操作，容器内部是无法操作的

```

### 4.5 初识DockerFile

Dockerfile 就是用来构建docker镜像的构建文件夹！命令脚本！

通过这个脚本可以生成镜像，镜像是一层一层的，脚本一个一个的命令，每个命令都是一层！

```shell
# 创建一个dockerfile文件，名字可以随机，建议使用dockerfile
# 文件中的内容  指令（大写） 参数
[root@hchost dockefile_test]# cat dockerfile1 
FROM centos

VOLUME ["volume01","volume02"]

CMD echo "------end-----"
CMD /bin/bash

# 这个每一个命令就是一层镜像

# 生成镜像
-f dockerfile1的路径
-t 生成镜像名称
.  当前路径			# 切记不要丢失
[root@hchost dockefile_test]# docker build -f dockerfile1 -t hc/centos:1.0 .
[root@hchost dockefile_test]# docker images
REPOSITORY            TAG                 IMAGE ID            CREATED             SIZE
hc/centos             1.0                 dec9e0704eb2        8 seconds ago       215MB
```

启动自己写的容器


这个卷和外部一定有一个同步的目录



查看卷挂载的路径

```shell
# docker inspect 容器ID
```



这种方式我们未来使用的十分多，因为我们通常会构建自己的镜像！

假设构建镜像时候没有挂载卷，要手动镜像挂载 -v 卷名:容器内路径

### 4.6 数据卷容器

--volumes-from

多个mysql数据同步



```shell
# 创建两个容器实现数据同步
# 创建docker01
docker run -it --name=docker01 centos
# 创建docker02
docker run -it --name=docker02 --volumes-from docker01 centos
# docker01 和docker02 的数据是同步的,docker01就是数据卷容器（父类），docker02是子类
# 还可以创建多个容器挂载docker01或docker02，删除了docker01，其余挂载的容器数据不会丢失
```

结论：

容器之间配置信息的传递，数据卷容器的生命周期一直持续到没有容器使用。

但是一旦持久化到了本地，这个时候，本地数据是不会删除的



## 5、DockerFile

### 5.1 DockerFile介绍

Dockerfile 就是用来构建docker镜像的构建文件夹！命令参数脚本！

**构建步骤：**

1. 编写一个dockerfile 文件
2. docker build 构建成为一个镜像
3. docker run 运行镜像
4. docker push 发布镜像（DockerHub、阿里云镜像仓库）

### 5.2 DockerFile构建过程

**基础知识：**

1. 每个保留关键字（指令）都必须是大写字母
2. 执行从上到下顺序执行
3.  #表示注释
4. 每一个指令都会创建提交一个新的镜像层，并提交！


dockerfile是面向开发的，我们以后要发布项目，做镜像，就需要编写dockerfile文件，这个文件十分简单！

Docker 镜像逐渐成为企业交付的标准，必须掌握！

**步骤：开发，部署，运维**

DockerFile：构建文件，定义了一切的步骤，相当于源代码

DockerImages：通过DockerFile 构建生成的镜像，最终发布和运行的产品！

Docker容器：容器就是镜像运行起来提供服务器

**构建容器前先开启内核路由转发,否则创建的容器无法连接网络** 

```sh
echo -e "net.ipv4.ip_forward = 1\nnet.ipv4.conf.default.rp_filter = 0 \nnet.ipv4.conf.all.rp_filter = 0" >> /etc/sysctl.conf
sysctl -p
```



### 5.3 Dockerfile的命令

```shell
FROM		# 基础镜像，一切从这里开始构建
MAINTAINER	# 镜像是谁写的，姓名+邮箱
RUN			# 镜像构建的时候需要运行的命令
ADD			# 添加内容，如基础镜像只是centos，添加个tomcat镜像 ,会解压
WORKDIR		# 镜像的工作目录
VOLUME		# 挂载的目录
EXPOSE		# 暴露端口配置
RUN			# 指定容器启动后要干的时间
CMD			# 指定这个容器启动的时候要运行的命令,只有最后一个会生效，可被替代 ls -a , ls -l 最后的生效，-l会替换-a
ENTRYPOINT	# 指定这个容器启动的时候要运行的命令，可以追加命令  ls -a , ls -l  追加到后面，最后执行的是  ls -a -l
ONBUILD		# 当构建一个被继承的 DockerFile 这个时候回运行ONBUILD 的指令，触发指令。
COPY		# 类似ADD，将我们的文件拷贝到镜像中
ENV			# 构建的时候设置环境变量
```



### 5.4 实战测试

Docker Hub 中 99% 镜像都是从这个基础镜像过来的，FROM scratch，然后配置需要的软件和配置来构建的。

```shell
FROM centos:7.2.1511 
MAINTAINER hc<zzrfhc@163.com> 
ENV MYPATH /usr/local 
WORKDIR $MYPATH 
RUN yum -y install vim 
RUN yum -y install net-tools 
EXPOSE 80 
CMD echo $MYPATH
CMD echo "----end----"
CMD /bin/bash
```

```shell
docker history 镜像ID		# 查看镜像的构建记录
```

> CMD 和 ENTRYPOINT 区别

```shell
CMD			# （覆盖）指定容器启动的时候要运行的命令，只有最后一个会生效，可被替代，只执行一次
ENTRYPOINT 	# （追加）指定容器启动的时候要运行的命令，可以追加命令

编写dockerfile1
FROM centos
CMD ["ls","-a"]

docker build -f dockerfile1 -t centosCMD .
docker run 镜像ID -l # 会报错，因为-l会替代CMD 的 ls -a

编写dockerfile2
FROM centos
ENTRYPOINT  ["ls","-a"]

docker build -f dockerfile2 -t centosENTRYPOINT .
docker run 镜像ID -l # 可以执行，相当于执行 ls -a -l

```

#### 5.4.1 实战：Tomcat镜像

1. 准备镜像文件，tomcat压缩包，JDK压缩包

```shell
# 下载tomcat压缩包
wget  https://downloads.apache.org/tomcat/tomcat-9/v9.0.36/bin/apache-tomcat-9.0.37.tar.gz 
# 下载jdk
wget --no-check-certificate --no-cookies --header "Cookie: oraclelicense=accept-securebackup-cookie" https://download.oracle.com/otn/java/jdk/8u261-b12/a4634525489241b9a9e1aa73d9e118e6/jdk-8u261-linux-x64.tar.gz
```



2. 编写Dockerfile文件，官方命名 ==Dockerfile== ，build会自动寻找这个文件，不需要 -f 指定了！

   ```shell
   FROM centos:7.2.1511
   MAINTAINER hc<zzrfhc@163.com> 
   COPY readme.txt /usr/local/readme.txt 
   ADD jdk-8u261-linux-x64.tar.gz /usr/local/ 
   ADD apache-tomcat-9.0.37.tar.gz /usr/local/ 
   RUN yum -y install vim
   RUN yum -y install net-tools 
   ENV MYPATH /usr/local 
   WORKDIR $MYPATH 
   ENV JAVA_HOME /usr/local/jdk1.8.0_261 
   ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar 
   ENV CATALINA_HOME /usr/local/apache-tomcat-9.0.37 
   ENV CATALINABASH /usr/local/apache-tomcat-9.0.37 
   ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin 
   EXPOSE 8080 
   CMD /usr/local/apache-tomcat-9.0.37/bin/startup.sh && tail -F /usr/local/apache-tomcat-9.0.37/bin/logs/catalina.out
   ```

3. 构建镜像，启动容器

   ```shell
   # 构建镜像
   [root@hchost home]# docker build -t tomcat_jdk .
   # 启动容器
   [root@hchost home]# docker run -d -p 8080:8080 --name tomcat_hc -v /home/tomcat/webapps:/usr/local/apache-tomcat-9.0.37/webapps -v /home/tomcat/logs:/usr/local/apache-tomcat-9.0.37/logs 93052c402a3e
   
   ```

   

#### 5.4.2 实战：Nginx镜像

```sh
# 创建容器部署方式

docker run -it --name my_nginx -p 8023:80 -v /opt/:/opt/ -v /usr/local/nginx/:/usr/local/nginx/ centos:7.6.1810 /bin/bash


yum install -y gcc gcc-c++ glibc make autoconf openssl openssl-devel libxslt-devel gd gd-devel GeoIP GeoIP-devel pcre pcre-devel

useradd -r serviceop -M -s /sbin/nologin

# 将这句注释掉 取消Debug编译模式，在179行
vim auto/cc/gcc
# CFLAGS="$CFLAGS -g"

./configure --prefix=/usr/local/nginx --user=serviceop --group=serviceop --with-http_stub_status_module --with-http_ssl_module

make

make install

cd /usr/local/nginx/sbin

./nginx

```



```bash
 # /home/docker_file/nginx_dockerfile
# 第一步 vim Dockerfile
FROM centos:7.6.1810
MAINTAINER hc<zzrfhc@163.com>

# ADD https://nginx.org/download/nginx-1.18.0.tar.gz .
ADD nginx-1.18.0.tar.gz /usr/local/src/

WORKDIR /usr/local/src/nginx-1.18.0

RUN yum install -y gcc gcc-c++ glibc make autoconf openssl openssl-devel
RUN yum install -y libxslt-devel gd gd-devel GeoIP GeoIP-devel pcre pcre-devel
RUN useradd -M -s /sbin/nologin -r serviceop

RUN ./configure --user=serviceop --group=serviceop --prefix=/usr/local/nginx --with-file-aio --with-http_ssl_module --with-http_realip_module --with-http_addition_module --with-http_xslt_module --with-http_image_filter_module --with-http_geoip_module --with-http_sub_module --with-http_dav_module --with-http_flv_module --with-http_mp4_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_auth_request_module --with-http_random_index_module --with-http_secure_link_module --with-http_degradation_module --with-http_stub_status_module
RUN make && make install
RUN ln -s /usr/local/nginx/sbin/* /usr/local/sbin/
EXPOSE 80


# 第二步 构建镜像
[root@hchost nginx_dockerfile]# docker build -t centos_nginx:v1 .

# 第三步 创建容器
[root@hchost nginx_dockerfile]# docker run -it --name nginx -v /usr/local/nginx/:/usr/local/nginx/ centos_nginx:1.18.0 /bin/bash


```



### 5.5 发布自己的镜像

> ### DockerHub

```shell
# 登陆
[root@hchost tomcat]# docker login --help

Usage:  docker login [OPTIONS] [SERVER]

Log in to a Docker registry.
If no server is specified, the default is defined by the daemon.

Options:
  -p, --password string   Password
      --password-stdin    Take the password from stdin
  -u, --username string   Username
  
[root@hchost tomcat]# docker login -u zzrfhc
Password: 
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
```

提交镜像

```shell
# docker push 镜像id
# 一般的格式是  docker push 作者/镜像id：版本号
[root@hchost tomcat]# docker push hc/tomcat_jdk:1.0

# 提交不成功是因为本地没有1.0这个tag

# 增加一个tag
docker tag 镜像ID 镜像名称：版本号
# 1、登陆docker hub
[root@hchost tomcat]# docker login -u zzrfhc

# 2、命名tag
[root@hchost tomcat]# docker tag 93052c402a3e zzrfhc/tomcat_jdk:1.0
# 注意：给自己镜像命名的时候格式应该是: docker注册用户名/镜像名,比如我的docker用户名为 zzrfhc,那么我的镜像tag就为 zzrfhc/镜像名称：版本号,不然是push不上去的


# 3、上传镜像
[root@hchost tomcat]# docker push zzrfhc/tomcat_jdk:1.0
The push refers to repository [docker.io/zzrfhc/tomcat_jdk]
6b08eaced85b: Pushing [>                                                  ]  1.078MB/79.96MB
4019bb2beee4: Pushing [>                                                  ]   2.16MB/134.9MB
ed6278e9867b: Pushing [===>                                               ]  1.025MB/15.63MB
df8af12b4375: Pushing [>                                                  ]  1.072MB/352.4MB
9e72b3c12000: Pushed 
a11c91bfd866: Pushing [>                                                  ]  544.8kB/194.6MB
```

> ### 阿里云镜像服务上

1. 登陆阿里云

2. 找到容器镜像服务

3. 创建命名空间

   容器镜像服务 ===> 命名空间 ===> 创建命名空间



4. 创建容器镜像

   容器镜像服务 ===> 镜像仓库 ===> 创建镜像仓库



5. 上传镜像

   1. **登录阿里云Docker Registry**

   ```
   $ sudo docker login --username=zzrfhc registry.cn-shanghai.aliyuncs.com
   ```

   用于登录的用户名为阿里云账号全名，密码为开通服务时设置的密码。

   您可以在访问凭证页面修改凭证密码。

   2. **从Registry中拉取镜像**

   ```
   $ sudo docker pull registry.cn-shanghai.aliyuncs.com/zzrfhc_docker/hc_docker:[镜像版本号]
   ```

   3. **将镜像推送到Registry**

   ```
   $ sudo docker login --username=zzrfhc registry.cn-shanghai.aliyuncs.com
   $ sudo docker tag [ImageId] registry.cn-shanghai.aliyuncs.com/zzrfhc_docker/hc_docker:[镜像版本号]
   $ sudo docker push registry.cn-shanghai.aliyuncs.com/zzrfhc_docker/hc_docker:[镜像版本号]
   ```

   请根据实际镜像信息替换示例中的[ImageId]和[镜像版本号]参数。

   4. **选择合适的镜像仓库地址**

   从ECS推送镜像时，可以选择使用镜像仓库内网地址。推送速度将得到提升并且将不会损耗您的公网流量。

   如果您使用的机器位于VPC网络，请使用 registry-vpc.cn-shanghai.aliyuncs.com 作为Registry的域名登录，并作为镜像命名空间前缀。

   5. **示例**

   使用"docker tag"命令重命名镜像，并将它通过专有网络地址推送至Registry。

   ```
   $ sudo docker imagesREPOSITORY                                                         TAG                 IMAGE ID            CREATED             VIRTUAL SIZEregistry.aliyuncs.com/acs/agent                                    0.7-dfb6816         37bb9c63c8b2        7 days ago          37.89 MB$ sudo docker tag 37bb9c63c8b2 registry-vpc.cn-shanghai.aliyuncs.com/acs/agent:0.7-dfb6816
   ```

   使用"docker images"命令找到镜像，将该镜像名称中的域名部分变更为Registry专有网络地址。

   ```
   $ sudo docker push registry-vpc.cn-shanghai.aliyuncs.com/acs/agent:0.7-dfb6816
   ```

   > 把自己制作的tomcat镜像上传到阿里云

   ```shell
   # 1、登陆
   [root@hchost tomcat]# docker login --username=zzrfhc registry.cn-shanghai.aliyuncs.com
   [root@hchost tomcat]# docker login --username=zzrfhc registry.cn-shanghai.aliyuncs.com/zzrfhc_docker/hc_docker
   Password: 
   WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
   Configure a credential helper to remove this warning. See
   https://docs.docker.com/engine/reference/commandline/login/#credentials-store
   
   Login Succeeded
   
   # 2、命名tag
   [root@hchost tomcat]# docker tag 93052c402a3e registry.cn-shanghai.aliyuncs.com/zzrfhc_docker/hc_docker/tomcat_jdk:1.0
   
   
   # 3、上传镜像
   [root@hchost ~]# docker push registry.cn-shanghai.aliyuncs.com/zzrfhc_docker/hc_docker/tomcat_jdk:1.0
   The push refers to repository [registry.cn-shanghai.aliyuncs.com/zzrfhc_docker/hc_docker/tomcat_jdk]
   6b08eaced85b: Pushing [>                                                  ]    535kB/79.96MB
   4019bb2beee4: Pushing [>                                                  ]  1.081MB/134.9MB
   ed6278e9867b: Pushing [==>                                                ]  867.3kB/15.63MB
   df8af12b4375: Pushing [>                                                  ]  1.072MB/352.4MB
   9e72b3c12000: Pushed 
   a11c91bfd866: Pushing [>                                                  ]  1.071MB/194.6MB
   ```

> 镜像上传到腾讯云

```sh
# 1、登陆
docker login --username=100015827964 ccr.ccs.tencentyun.com

# 2、拉取
docker pull ccr.ccs.tencentyun.com/zzrfhc_docker/hc_docker:[tag]

# 3、推送
docker login --username=100015827964 ccr.ccs.tencentyun.com
docker tag [ImageId] ccr.ccs.tencentyun.com/zzrfhc_docker/hc_docker:[tag]
docker push ccr.ccs.tencentyun.com/zzrfhc_docker/hc_docker:[tag]

docker tag $(docker images|grep pod|awk '{print $3}') ccr.ccs.tencentyun.com/zzrfhc_docker/pod-infrastructure:laster

docker push $(docker images|grep pod|awk '{print $1":"$2}')
```





### 5.6 docker 仓库（registry）

#### 5.6.1 创建仓库

1、创建仓库

```sh
docker run -d -p 5000:5000 --restart=always --name registry -v /opt/myregistry:/var/lib/registry  registry 
```

2、修改配置文件，使之支持http

```sh
[root@docker01 ~]# cat  /etc/docker/daemon.json 
{
  "registry-mirrors": ["https://a5dwl7z9.mirror.aliyuncs.com"],
  "insecure-registries": ["i8023.icu:5000"]
}
```

重启docker让修改生效

```sh
[root@docker01 ~]# systemctl restart  docker.service
```

3、修改镜像标签

```sh
[root@docker01 ~]# docker tag  busybox:latest  i8023.icu:5000/clsn/busybox:1.0
[root@docker01 ~]# docker images
REPOSITORY                      TAG                 IMAGE ID            CREATED             SIZE
centos6-ssh                     latest              3c2b1e57a0f5        18 hours ago        393MB
httpd                           2.4                 2e202f453940        6 days ago          179MB
10.0.0.100:5000/clsn/busybox    1.0                 5b0d59026729        8 days ago          1.15MB
```

4、将新打标签的镜像上传镜像到仓库

```sh
[root@docker01 ~]# docker push   i8023.icu:5000/clsn/busybox
```

#### 5.6.2 带basic认证的仓库

1、安装加密工具

```sh
[root@docker01 clsn]# yum install httpd-tools  -y
```

2、设置认证密码

```sh
mkdir /opt/registry-var/auth/ -p
htpasswd  -Bbn clsn 123456  > /opt/registry-var/auth/htpasswd
```

3、启动容器，在启动时传入认证参数

```sh
docker run -d -p 5000:5000 -v /opt/registry-var/auth/:/auth/ -e "REGISTRY_AUTH=htpasswd" -e "REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm" -e REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd registry
```

4、使用验证用户测试

```sh
# 登陆用户
[root@docker01 ~]# docker login 10.0.0.100:5000 
Username: clsn  
Password: 123456
Login Succeeded
# 推送镜像到仓库
[root@docker01 ~]# docker push 10.0.0.100:5000/clsn/busybox 
The push refers to repository [10.0.0.100:5000/clsn/busybox]
4febd3792a1f: Pushed 
1.0: digest: sha256:4cee1979ba0bf7db9fc5d28fb7b798ca69ae95a47c5fecf46327720df4ff352d size: 527
#认证文件的保存位置
[root@docker01 ~]# cat .docker/config.json 
```

### 5.7 企业级镜像仓库harbor

1、安装docker、docker-compose

下载 harbor

```sh
cd /opt && https://storage.googleapis.com/harbor-releases/harbor-offline-installer-v1.3.0.tgz
tar xf harbor-offline-installer-v1.3.0.tgz
```

2、修改主机及web界面密码

```sh
[root@docker01 harbor]# vim harbor.cfg 
    ···
    hostname = 10.0.0.100
    harbor_admin_password = Harbor12345
    ···
```

3、执行安装脚本

```sh
[root@docker01 harbor]# ./install.sh
```



4、镜像推送到仓库的指定项目

```sh
[root@docker02 ~]# docker  tag centos:6.8  10.0.0.100/clsn/centos6.8:1.0
[root@docker02 ~]#  
[root@docker02 ~]# docker images 
REPOSITORY                  TAG                 IMAGE ID            CREATED             SIZE
busybox                     latest              5b0d59026729        8 days ago          1.15MB
10.0.0.100/clsn/centos6.8   1.0                 6704d778b3ba        2 months ago        195MB
centos                      6.8                 6704d778b3ba        2 months ago        195MB
[root@docker02 ~]# docker login 10.0.0.100
Username: admin
Password: 
Login Succeeded
```

5、推送镜像

```sh
[root@docker02 ~]# docker push 10.0.0.100/clsn/centos6.8 
The push refers to repository [10.0.0.100/clsn/centos6.8]
e00c9229b481: Pushing  13.53MB/194.5MB
```





### 5.8 小结：











## 6、Docker 网络

### 6.1 理解Docker0

```shell
# docker 是如何处理容器访问的
# 启动一个tomcat容器
[root@hchost ~]# docker run -d -P --name tomcat01 tomcat

# 查看容器的内部网络地址，发现容器启动的时候会得到一个 eth0@if97 IP地址，docker分配的。
[root@hchost ~]# docker exec -it tomcat01 ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
96: eth0@if97: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:ac:11:00:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.17.0.2/16 brd 172.17.255.255 scope global eth0
       valid_lft forever preferred_lft forever
# 宿主机和创建的容器可以互相ping通
```

> 原理

1. 我们每启动一个docker容器，docker就会给docker容器分配一个ip，我们只要安装了docker，就会有一个网卡docker0，桥接模式，使用的技术是veth-pair技术！

2. 再启动一个容器，发现又多了一对网卡

   ```shell
   # 我们发现这个容器带来网卡，都是一对对的
   # veth-pair 就是一对的虚拟设备接口，他们都是成对出现的，一般连着协议，一段被此相连
   # 正因为有这个特性，veth-pair 充当一个桥染，连接各种虚拟网络设备的
   # Openstac,Docker容器之间的连接，OSV的连接，都是使用evth-pair技术
   ```

   ==测试发现两个容器间也是可以 ping 通的==，都是通过桥接到docker0。

3. 通过图形看两个容器间的通信



   tomcat01 和 tomcat02 之间互ping，其实是共用的一个路由器，即通过Docker0的，，大概是两种方式，一个是记录到路由表，一个是采用广播的方式。 

   所有的容器不指定网络的情况下，都是docker0路由的，docker会给我们的容器分配一个默认的可用IP。

> 小结

​	docker 使用的是Linux桥接，宿主机中是一个Docker容器的网桥 docker0



​	Docker中的所有的网络接口都是虚拟的。虚拟的转发效率高!（内网传递文件）

​	只要容器删除，对应的网桥就没有了

### 6.2 自定义网络

> 查看所有的docker网络



**网络模式**

- bridge：桥接 docker(默认，自己创建也使用bridge模式）

- none：不配置网络

- host：和宿主机共享网络

- container：容器网络连通！（用的少！局限很大，不建议）

**测试**

```shell
# 我们直接启动的命令，默认是有 --net bridge  这个就是我们的docker0
# 下面两个是一样的
docker run -d -P --name tomcat01 tomcat
docker run -d -P --name tomcat01 --net bridge tomcat

# docker0特点，默认，域名不能访问，--link可以打通连接！

# 我们可以自定义一个网络
# --driver bridge			默认的桥接
# --subnet 192.168.0.0/16	子网掩码，IP地址可以从192.168.0.2 到 192.168.255.254
# --gateway 192.168.0.1		网关
[root@hchost ~]# docker network create --driver bridge --subnet 192.168.0.0/16 --gateway 192.168.0.1 mynet
48ceabfc6269dbc1c5421a458dcaa2c9222ec37af976e2c7d188f1cc45f50229
[root@hchost ~]# docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
3c3c9f3b789c        bridge              bridge              local
68621de96c42        host                host                local
48ceabfc6269        mynet               bridge              local
f51bc489a915        none                null                local
```

查看自己创建的网络



创建两个容器，查看网络配置

```shell
[root@hchost ~]# docker network inspect mynet
[
    {
        "Name": "mynet",
        "Id": "48ceabfc6269dbc1c5421a458dcaa2c9222ec37af976e2c7d188f1cc45f50229",
        "Created": "2020-08-08T01:03:53.282207022+08:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "192.168.0.0/16",
                    "Gateway": "192.168.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "376a9ab5ae880e98d6b7eeaaff720818ea8c3f1e48c48ff062a740bf2bdb5722": {
                "Name": "tomcat02",
                "EndpointID": "dd5c473d404d192aae4bb29065e557f4ba21dfc1b9bf4255bb16bf8fdf1ae4f1",
                "MacAddress": "02:42:c0:a8:00:03",
                "IPv4Address": "192.168.0.3/16",
                "IPv6Address": ""
            },
            "9132c85ae598773568c55b4ea666aef73f92c43862aff403e826c3ed6286f3e5": {
                "Name": "tomcat01",
                "EndpointID": "8cefa99faaf39ff02e33566a21a0b6c4f0cd4eb4b2f5e2c45ee685c050304813",
                "MacAddress": "02:42:c0:a8:00:02",
                "IPv4Address": "192.168.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]


# 再次测试 ping连接，不使用--link也可以ping通
[root@hchost ~]# docker exec -it tomcat01 ping 192.168.0.3
PING 192.168.0.3 (192.168.0.3) 56(84) bytes of data.
64 bytes from 192.168.0.3: icmp_seq=1 ttl=64 time=0.077 ms
64 bytes from 192.168.0.3: icmp_seq=2 ttl=64 time=0.046 ms
^C
--- 192.168.0.3 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 999ms
rtt min/avg/max/mdev = 0.046/0.061/0.077/0.017 ms

[root@hchost ~]# docker exec -it tomcat01 ping tomcat02
PING tomcat02 (192.168.0.3) 56(84) bytes of data.
64 bytes from tomcat02.mynet (192.168.0.3): icmp_seq=1 ttl=64 time=0.042 ms
64 bytes from tomcat02.mynet (192.168.0.3): icmp_seq=2 ttl=64 time=0.048 ms
^C
--- tomcat02 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1000ms
rtt min/avg/max/mdev = 0.042/0.045/0.048/0.003 ms

[root@hchost ~]# docker exec -it tomcat02 ping tomcat01
PING tomcat01 (192.168.0.2) 56(84) bytes of data.
64 bytes from tomcat01.mynet (192.168.0.2): icmp_seq=1 ttl=64 time=0.041 ms
64 bytes from tomcat01.mynet (192.168.0.2): icmp_seq=2 ttl=64 time=0.045 ms
^C
--- tomcat01 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 7ms
rtt min/avg/max/mdev = 0.041/0.043/0.045/0.002 ms
```

我们自定义的网络docker都已经帮我们维护好了对应的关系，推荐我们平时这样使用网络！

**好处：**
redis-不同的集群使用不同的网络，保证集群是安全和健康的

mysql-不同的集群使用不同的网络，保证集群是安全和健康的



### 6.3 网络连通

```shell
# Usage:  docker network connect [OPTIONS] NETWORK CONTAINER

# 测试打通 tomcat01 mynet
[root@hchost ~]# docker network connect mynet tomcat01

# 查看mynet信息
```



```powershell
连通之后就是将tomcat01放到mynet网络下

即所谓 的 一个容器，两个IP地址，如 阿里云，有 公网IP和内网IP
```

**正常网卡和网卡直接是不能打通的，但是容器和网卡直接可以**



结论：假设要跨网络操作别人，就需要使用docker network connect 连通...

### 6.4 实战：部署Redis集群

启动6个容器，3主3从，Master-Slave



```shell
# 创建网卡
docker network create redis --subnet 172.38.0.0/16

# 通过脚本创建六个redis配置
for port in $(seq 1 6); \
do \
mkdir -p /mydata/redis/node-${port}/conf
touch /mydata/redis/node-${port}/conf/redis.conf
cat << EOF >/mydata/redis/node-${port}/conf/redis.conf
port 6379
bind 0.0.0.0
cluster-enabled yes
cluster-config-file nodes.conf
cluster-node-timeout 5000
cluster-announce-ip 172.38.0.1${port}
cluster-announce-port 6379
cluster-announce-bus-port 16379
appendonly yes
EOF
done

for port in $(seq 1 6); \
do \
docker run -p 637${port}:6379 -p 1637${port}:16379 --name redis-${port} \
-v /mydata/redis/node-${port}/data:/data \
-v /mydata/redis/node-${port}/conf/redis.conf:/etc/redis/redis.conf \
-d --net redis --ip 172.38.0.1${port} redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf \
done

docker run -p 6371:6379 -p 16371:16379 --name redis-1 \
-v /mydata/redis/node-1/data:/data \
-v /mydata/redis/node-1/conf/redis.conf:/etc/redis/redis.conf \
-d --net redis --ip 172.38.0.11 redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf

[root@hchost /]# for port in $(seq 1 6); \
> do \
> mkdir -p /mydata/redis/node-${port}/conf 
> touch /mydata/redis/node-${port}/conf/redis.conf
> cat << EOF >/mydata/redis/node-${port}/conf/redis.conf
> port 6379
> bind 0.0.0.0
> cluster-enabled yes
> cluster-config-file nodes.conf
> cluster-node-timeout 5000
> cluster-announce-ip 172.38.0.1${port}
> cluster-announce-port 6379
> cluster-announce-bus-port 16379
> appendonly yes
> EOF
> done

[root@hchost conf]# for port in $(seq 1 6); \
> do \
> docker run -p 637${port}:6379 -p 1637${port}:16379 --name redis-${port} -v /mydata/redis/node-${port}/data:/data -v /mydata/redis/node-${port}/conf/redis.conf:/etc/redis/redis.conf -d --net redis --ip 172.38.0.1${port} redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf
> done

# 进入集群，随便进一个即可，redis 是用的sh
[root@hchost conf]# docker exec -it redis-1 /bin/sh

# 创建集群 
redis-cli --cluster create 172.38.0.11:6379 172.38.0.12:6379 172.38.0.13:6379 172.38.0.14:6379 172.38.0.15:6379 172.38.0.16:6379 --cluster-replicas 1

/data # redis-cli --cluster create 172.38.0.11:6379 172.38.0.12:6379 172.38.0.13:6379 172.38.0.14:6379 172.38.0.15:6379 172.38.0.16:6379 --cluster-replicas 1
>>> Performing hash slots allocation on 6 nodes...
Master[0] -> Slots 0 - 5460
Master[1] -> Slots 5461 - 10922
Master[2] -> Slots 10923 - 16383
Adding replica 172.38.0.15:6379 to 172.38.0.11:6379
Adding replica 172.38.0.16:6379 to 172.38.0.12:6379
Adding replica 172.38.0.14:6379 to 172.38.0.13:6379
M: 6851ddbc71ce9ecceb50b4b9ca017cf1e730225f 172.38.0.11:6379
   slots:[0-5460] (5461 slots) master
M: ef00325f6245362096b1c09b79efd5e624be1441 172.38.0.12:6379
   slots:[5461-10922] (5462 slots) master
M: 41bc1a806a949289e807cd3e4e5277d20a0c7f60 172.38.0.13:6379
   slots:[10923-16383] (5461 slots) master
S: 2647b2c74f1a819337e7af3a7e8972bf59a044ef 172.38.0.14:6379
   replicates 41bc1a806a949289e807cd3e4e5277d20a0c7f60
S: 6d02717d00ac2529fc14a5779bc1d150cdd32d6e 172.38.0.15:6379
   replicates 6851ddbc71ce9ecceb50b4b9ca017cf1e730225f
S: 7f49b4f3fcc19882f82430d3f4121f276a3531d9 172.38.0.16:6379
   replicates ef00325f6245362096b1c09b79efd5e624be1441
Can I set the above configuration? (type 'yes' to accept): yes
>>> Nodes configuration updated
>>> Assign a different config epoch to each node
>>> Sending CLUSTER MEET messages to join the cluster
Waiting for the cluster to join
...
>>> Performing Cluster Check (using node 172.38.0.11:6379)
M: 6851ddbc71ce9ecceb50b4b9ca017cf1e730225f 172.38.0.11:6379
   slots:[0-5460] (5461 slots) master
   1 additional replica(s)
S: 2647b2c74f1a819337e7af3a7e8972bf59a044ef 172.38.0.14:6379
   slots: (0 slots) slave
   replicates 41bc1a806a949289e807cd3e4e5277d20a0c7f60
M: ef00325f6245362096b1c09b79efd5e624be1441 172.38.0.12:6379
   slots:[5461-10922] (5462 slots) master
   1 additional replica(s)
S: 7f49b4f3fcc19882f82430d3f4121f276a3531d9 172.38.0.16:6379
   slots: (0 slots) slave
   replicates ef00325f6245362096b1c09b79efd5e624be1441
M: 41bc1a806a949289e807cd3e4e5277d20a0c7f60 172.38.0.13:6379
   slots:[10923-16383] (5461 slots) master
   1 additional replica(s)
S: 6d02717d00ac2529fc14a5779bc1d150cdd32d6e 172.38.0.15:6379
   slots: (0 slots) slave
   replicates 6851ddbc71ce9ecceb50b4b9ca017cf1e730225f
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.

# 进入集群  redis-cli是单机  -c 是集群
/data # redis-cli -c
127.0.0.1:6379> 

# 查看集群信息  cluster info
127.0.0.1:6379> cluster info
cluster_state:ok
cluster_slots_assigned:16384
cluster_slots_ok:16384
cluster_slots_pfail:0
cluster_slots_fail:0
cluster_known_nodes:6
cluster_size:3
cluster_current_epoch:6
cluster_my_epoch:1
cluster_stats_messages_ping_sent:2331
cluster_stats_messages_pong_sent:2349
cluster_stats_messages_sent:4680
cluster_stats_messages_ping_received:2344
cluster_stats_messages_pong_received:2331
cluster_stats_messages_meet_received:5
cluster_stats_messages_received:4680

# 查看节点
127.0.0.1:6379> cluster nodes
2647b2c74f1a819337e7af3a7e8972bf59a044ef 172.38.0.14:6379@16379 slave 41bc1a806a949289e807cd3e4e5277d20a0c7f60 0 1596858049589 4 connected
ef00325f6245362096b1c09b79efd5e624be1441 172.38.0.12:6379@16379 master - 0 1596858049000 2 connected 5461-10922
7f49b4f3fcc19882f82430d3f4121f276a3531d9 172.38.0.16:6379@16379 slave ef00325f6245362096b1c09b79efd5e624be1441 0 1596858050000 6 connected
41bc1a806a949289e807cd3e4e5277d20a0c7f60 172.38.0.13:6379@16379 master - 0 1596858050000 3 connected 10923-16383
6d02717d00ac2529fc14a5779bc1d150cdd32d6e 172.38.0.15:6379@16379 slave 6851ddbc71ce9ecceb50b4b9ca017cf1e730225f 0 1596858050791 5 connected
6851ddbc71ce9ecceb50b4b9ca017cf1e730225f 172.38.0.11:6379@16379 myself,master - 0 1596858049000 1 connected 0-5460

# 测试
# 看到是分配到12主机上
172.38.0.13:6379> set c d
-> Redirected to slot [7365] located at 172.38.0.12:6379
OK

# 停止12容器，获取value,发现从机16处理了
127.0.0.1:6379> get c
-> Redirected to slot [7365] located at 172.38.0.16:6379
"d"
```





## 7、Docker Compose

### 7.1 简介

==轻松高效的管理多个容器==

> 官网文档   https://docs.docker.com/compose/ 

Compose is a tool for defining and running multi-container Docker applications. With Compose, you use a YAML file to configure your application’s services. Then, with a single command, you create and start all the services from your configuration. To learn more about all the features of Compose, see [the list of features](https://docs.docker.com/compose/#features).

Compose works in all environments: production, staging, development, testing, as well as CI workflows. You can learn more about each case in [Common Use Cases](https://docs.docker.com/compose/#common-use-cases).

Using Compose is basically a three-step process:

1. Define your app’s environment with a `Dockerfile` so it can be reproduced anywhere.
   - Dockerfile 保证我们的容器在任何地方可以运行
2. Define the services that make up your app in `docker-compose.yml` so they can be run together in an isolated environment.
3. Run `docker-compose up` and Compose starts and runs your entire app.

- 使用Compose包含三个最基本步骤：

  1、定义Dockerfile文件，该文件定义了应用环境（镜像的构建）

  2、定义docker-compose.yml文件，该文件定义了组成应用的服务

  3、运行docker-compose up命令

  

  \# 启动所有服务

  $ docker-compose up

  \# 关闭所有服务

  $ docker-compose stop

**作用：批量容器编排。**

> 自己的理解

Compose 是 Docker 官方的开源项目。需要安装！

Dockerfile 让程序在任何地方运行，web服务。redis、mysql、nginx….多个容器。

```yaml
version: '2.0'
services:
  web:
    build: .
    ports:
    - "5000:5000"
    volumes:
    - .:/code
    - logvolume01:/var/log
    links:
    - redis
  redis:
    image: redis
volumes:
  logvolume01: {}
```

Compose：重要的概念

- 服务services，容器，应用。(web，redis，mysql…)

- 项目project，一组关联的容器。博客。web、mysql

### 7.2 安装

```shell
# 下载，官方的地址估计很慢
sudo curl -L "https://github.com/docker/compose/releases/download/1.26.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

# 这个估计快点
curl -L https://get.daocloud.io/docker/compose/releases/domnload/1.25.5/docker-compose-`uname-s`-`uname-m` > /usr/local/bin/docker-compose

# 如果第一步没有下载下来，我们就手动去网址下载即可！
# 地址：https://github.com/docker/compose/releases
# 下载：docker-compose-Linux-×86.64
# 然后将文件上转到/usr/local/bin/文件夹下，然后将其重命名为docker-compose

# 第二步，修改此文件的权限，增加可执行
sudo chmod +x /usr/local/bin/docker-compose

# 第三步，查看是否安装成功
docker-compose --version
[root@hchost bin]# docker-compose version
docker-compose version 1.26.2, build eefe0d31
docker-py version: 4.2.2
CPython version: 3.7.7
OpenSSL version: OpenSSL 1.1.0l  10 Sep 2019


# 创建软连接，加到环境变量中，可以在别的路径下直接使用docker-compose
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose

# 卸载docker-compose
# To uninstall Docker Compose if you installed using curl:
sudo rm /usr/local/bin/docker-compose
# To uninstall Docker Compose if you installed using pip:
pip uninstall docker-compose
```

### 7.3 体验

地址： https://docs.docker.com/compose/gettingstarted/ 

python应用。计数器。redis！

1、应用app.py	 requirements.txt 

```shell
# 创建测试目录
[root@hchost home]# mkdir composetest
[root@hchost home]# cd composetest/

# 创建app.py文件
import time

import redis
from flask import Flask

app = Flask(__name__)
cache = redis.Redis(host='redis', port=6379)

def get_hit_count():
    retries = 5
    while True:
        try:
            return cache.incr('hits')
        except redis.exceptions.ConnectionError as exc:
            if retries == 0:
                raise exc
            retries -= 1
            time.sleep(0.5)


@app.route('/')
def hello():
    count = get_hit_count()
    return 'Hello World! I have been seen {} times.\n'.format(count)
    
# 创建导入包文件 requirements.txt
flask
redis

```

2、Dockerfile 应用打包为镜像

```shell
# 创建Dockerfile
FROM python:3.7-alpine
WORKDIR /code
ENV FLASK_APP app.py
ENV FLASK_RUN_HOST 0.0.0.0
RUN apk add --no-cache gcc musl-dev linux-headers
COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt
EXPOSE 5000
COPY . .
CMD ["flask", "run"]
```

3、Docker-compose yaml文件（定义整个服务，需要的环境。web、redis)完整的上线服务！

```shell
# 创建docker-compose.yml
version: '3'
services:
  web:
    build: .
    ports:
      - "5000:5000"
  redis:
    image: "redis:alpine"
```

4、启动compose 项目（ docker-compose up )

启动成功后看到对应的服务起来了



默认的服务名：文件名_ 服务名_ num

网络也是默认创建好的



docker-compose

以前都是单个docker run 启动容器。

docker-compose。通过 docker-compose 编写yaml配置文件、可以通过compose 一键启动所有服务，停止。！

**Docker小结：**

1、Docker镜像。run=>容器

2、DockerFile 构建镜像（服务打包）

3、docker-compose 启动项目（编排、多个微服务/环境）

4、Docker网络！

### 7.4 yaml规则

> 核心		 https://docs.docker.com/compose/compose-file/ 

```shell
# 3层
version:''	# 版本 
services:	# 服务 
	服务1：web 
	#服务配置 
	images 
	build 
	network
    ......
	服务2：redis
    ......
	服务3：db
    ......

# 其他配置网络/卷、全局规则 
volumes:
networks:
configs:
```



```shell
Compose和Docker兼容性：
    Compose 文件格式有3个版本,分别为1, 2.x 和 3.x
    目前主流的为 3.x 其支持 docker 1.13.0 及其以上的版本
 
常用参数：
    version           # 指定 compose 文件的版本
    services          # 定义所有的 service 信息, services 下面的第一级别的 key 既是一个 service 的名称
 
        build                 # 指定包含构建上下文的路径, 或作为一个对象，该对象具有 context 和指定的 dockerfile 文件以及 args 参数值
            context               # context: 指定 Dockerfile 文件所在的路径
            dockerfile            # dockerfile: 指定 context 指定的目录下面的 Dockerfile 的名称(默认为 Dockerfile)
            args                  # args: Dockerfile 在 build 过程中需要的参数 (等同于 docker container build --build-arg 的作用)
            cache_from            # v3.2中新增的参数, 指定缓存的镜像列表 (等同于 docker container build --cache_from 的作用)
            labels                # v3.3中新增的参数, 设置镜像的元数据 (等同于 docker container build --labels 的作用)
            shm_size              # v3.5中新增的参数, 设置容器 /dev/shm 分区的大小 (等同于 docker container build --shm-size 的作用)
 
        command               # 覆盖容器启动后默认执行的命令, 支持 shell 格式和 [] 格式
 
        configs               # 不知道怎么用
 
        cgroup_parent         # 不知道怎么用
 
        container_name        # 指定容器的名称 (等同于 docker run --name 的作用)
 
        credential_spec       # 不知道怎么用
 
        deploy                # v3 版本以上, 指定与部署和运行服务相关的配置, deploy 部分是 docker stack 使用的, docker stack 依赖 docker swarm
            endpoint_mode         # v3.3 版本中新增的功能, 指定服务暴露的方式
                vip                   # Docker 为该服务分配了一个虚拟 IP(VIP), 作为客户端的访问服务的地址
                dnsrr                 # DNS轮询, Docker 为该服务设置 DNS 条目, 使得服务名称的 DNS 查询返回一个 IP 地址列表, 客户端直接访问其中的一个地址
            labels                # 指定服务的标签，这些标签仅在服务上设置
            mode                  # 指定 deploy 的模式
                global                # 每个集群节点都只有一个容器
                replicated            # 用户可以指定集群中容器的数量(默认)
            placement             # 不知道怎么用
            replicas              # deploy 的 mode 为 replicated 时, 指定容器副本的数量
            resources             # 资源限制
                limits                # 设置容器的资源限制
                    cpus: "0.5"           # 设置该容器最多只能使用 50% 的 CPU 
                    memory: 50M           # 设置该容器最多只能使用 50M 的内存空间 
                reservations          # 设置为容器预留的系统资源(随时可用)
                    cpus: "0.2"           # 为该容器保留 20% 的 CPU
                    memory: 20M           # 为该容器保留 20M 的内存空间
            restart_policy        # 定义容器重启策略, 用于代替 restart 参数
                condition             # 定义容器重启策略(接受三个参数)
                    none                  # 不尝试重启
                    on-failure            # 只有当容器内部应用程序出现问题才会重启
                    any                   # 无论如何都会尝试重启(默认)
                delay                 # 尝试重启的间隔时间(默认为 0s)
                max_attempts          # 尝试重启次数(默认一直尝试重启)
                window                # 检查重启是否成功之前的等待时间(即如果容器启动了, 隔多少秒之后去检测容器是否正常, 默认 0s)
            update_config         # 用于配置滚动更新配置
                parallelism           # 一次性更新的容器数量
                delay                 # 更新一组容器之间的间隔时间
                failure_action        # 定义更新失败的策略
                    continue              # 继续更新
                    rollback              # 回滚更新
                    pause                 # 暂停更新(默认)
                monitor               # 每次更新后的持续时间以监视更新是否失败(单位: ns|us|ms|s|m|h) (默认为0)
                max_failure_ratio     # 回滚期间容忍的失败率(默认值为0)
                order                 # v3.4 版本中新增的参数, 回滚期间的操作顺序
                    stop-first            #旧任务在启动新任务之前停止(默认)
                    start-first           #首先启动新任务, 并且正在运行的任务暂时重叠
            rollback_config       # v3.7 版本中新增的参数, 用于定义在 update_config 更新失败的回滚策略
                parallelism           # 一次回滚的容器数, 如果设置为0, 则所有容器同时回滚
                delay                 # 每个组回滚之间的时间间隔(默认为0)
                failure_action        # 定义回滚失败的策略
                    continue              # 继续回滚
                    pause                 # 暂停回滚
                monitor               # 每次回滚任务后的持续时间以监视失败(单位: ns|us|ms|s|m|h) (默认为0)
                max_failure_ratio     # 回滚期间容忍的失败率(默认值0)
                order                 # 回滚期间的操作顺序
                    stop-first            # 旧任务在启动新任务之前停止(默认)
                    start-first           # 首先启动新任务, 并且正在运行的任务暂时重叠
 
            注意：
                支持 docker-compose up 和 docker-compose run 但不支持 docker stack deploy 的子选项
                security_opt  container_name  devices  tmpfs  stop_signal  links    cgroup_parent
                network_mode  external_links  restart  build  userns_mode  sysctls
 
        devices               # 指定设备映射列表 (等同于 docker run --device 的作用)
 
        depends_on            # 定义容器启动顺序 (此选项解决了容器之间的依赖关系， 此选项在 v3 版本中 使用 swarm 部署时将忽略该选项)
            示例：
                docker-compose up 以依赖顺序启动服务，下面例子中 redis 和 db 服务在 web 启动前启动
                默认情况下使用 docker-compose up web 这样的方式启动 web 服务时，也会启动 redis 和 db 两个服务，因为在配置文件中定义了依赖关系
 
                version: '3'
                services:
                    web:
                        build: .
                        depends_on:
                            - db      
                            - redis  
                    redis:
                        image: redis
                    db:
                        image: postgres                             
 
        dns                   # 设置 DNS 地址(等同于 docker run --dns 的作用)
 
        dns_search            # 设置 DNS 搜索域(等同于 docker run --dns-search 的作用)
 
        tmpfs                 # v2 版本以上, 挂载目录到容器中, 作为容器的临时文件系统(等同于 docker run --tmpfs 的作用, 在使用 swarm 部署时将忽略该选项)
 
        entrypoint            # 覆盖容器的默认 entrypoint 指令 (等同于 docker run --entrypoint 的作用)
 
        env_file              # 从指定文件中读取变量设置为容器中的环境变量, 可以是单个值或者一个文件列表, 如果多个文件中的变量重名则后面的变量覆盖前面的变量, environment 的值覆盖 env_file 的值
            文件格式：
                RACK_ENV=development 
 
        environment           # 设置环境变量， environment 的值可以覆盖 env_file 的值 (等同于 docker run --env 的作用)
 
        expose                # 暴露端口, 但是不能和宿主机建立映射关系, 类似于 Dockerfile 的 EXPOSE 指令
 
        external_links        # 连接不在 docker-compose.yml 中定义的容器或者不在 compose 管理的容器(docker run 启动的容器, 在 v3 版本中使用 swarm 部署时将忽略该选项)
 
        extra_hosts           # 添加 host 记录到容器中的 /etc/hosts 中 (等同于 docker run --add-host 的作用)
 
        healthcheck           # v2.1 以上版本, 定义容器健康状态检查, 类似于 Dockerfile 的 HEALTHCHECK 指令
            test                  # 检查容器检查状态的命令, 该选项必须是一个字符串或者列表, 第一项必须是 NONE, CMD 或 CMD-SHELL, 如果其是一个字符串则相当于 CMD-SHELL 加该字符串
                NONE                  # 禁用容器的健康状态检测
                CMD                   # test: ["CMD", "curl", "-f", "http://localhost"]
                CMD-SHELL             # test: ["CMD-SHELL", "curl -f http://localhost || exit 1"] 或者　test: curl -f https://localhost || exit 1
            interval: 1m30s       # 每次检查之间的间隔时间
            timeout: 10s          # 运行命令的超时时间
            retries: 3            # 重试次数
            start_period: 40s     # v3.4 以上新增的选项, 定义容器启动时间间隔
            disable: true         # true 或 false, 表示是否禁用健康状态检测和　test: NONE 相同
 
        image                 # 指定 docker 镜像, 可以是远程仓库镜像、本地镜像
 
        init                  # v3.7 中新增的参数, true 或 false 表示是否在容器中运行一个 init, 它接收信号并传递给进程
 
        isolation             # 隔离容器技术, 在 Linux 中仅支持 default 值
 
        labels                # 使用 Docker 标签将元数据添加到容器, 与 Dockerfile 中的 LABELS 类似
 
        links                 # 链接到其它服务中的容器, 该选项是 docker 历史遗留的选项, 目前已被用户自定义网络名称空间取代, 最终有可能被废弃 (在使用 swarm 部署时将忽略该选项)
 
        logging               # 设置容器日志服务
            driver                # 指定日志记录驱动程序, 默认 json-file (等同于 docker run --log-driver 的作用)
            options               # 指定日志的相关参数 (等同于 docker run --log-opt 的作用)
                max-size              # 设置单个日志文件的大小, 当到达这个值后会进行日志滚动操作
                max-file              # 日志文件保留的数量
 
        network_mode          # 指定网络模式 (等同于 docker run --net 的作用, 在使用 swarm 部署时将忽略该选项)         
 
        networks              # 将容器加入指定网络 (等同于 docker network connect 的作用), networks 可以位于 compose 文件顶级键和 services 键的二级键
            aliases               # 同一网络上的容器可以使用服务名称或别名连接到其中一个服务的容器
            ipv4_address      # IP V4 格式
            ipv6_address      # IP V6 格式
 
            示例:
                version: '3.7'
                services: 
                    test: 
                        image: nginx:1.14-alpine
                        container_name: mynginx
                        command: ifconfig
                        networks: 
                            app_net:                                # 调用下面 networks 定义的 app_net 网络
                            ipv4_address: 172.16.238.10
                networks:
                    app_net:
                        driver: bridge
                        ipam:
                            driver: default
                            config:
                                - subnet: 172.16.238.0/24
 
        pid: 'host'           # 共享宿主机的 进程空间(PID)
 
        ports                 # 建立宿主机和容器之间的端口映射关系, ports 支持两种语法格式
            SHORT 语法格式示例:
                - "3000"                            # 暴露容器的 3000 端口, 宿主机的端口由 docker 随机映射一个没有被占用的端口
                - "3000-3005"                       # 暴露容器的 3000 到 3005 端口, 宿主机的端口由 docker 随机映射没有被占用的端口
                - "8000:8000"                       # 容器的 8000 端口和宿主机的 8000 端口建立映射关系
                - "9090-9091:8080-8081"
                - "127.0.0.1:8001:8001"             # 指定映射宿主机的指定地址的
                - "127.0.0.1:5000-5010:5000-5010"   
                - "6060:6060/udp"                   # 指定协议
 
            LONG 语法格式示例:(v3.2 新增的语法格式)
                ports:
                    - target: 80                    # 容器端口
                      published: 8080               # 宿主机端口
                      protocol: tcp                 # 协议类型
                      mode: host                    # host 在每个节点上发布主机端口,  ingress 对于群模式端口进行负载均衡
 
        secrets               # 不知道怎么用
 
        security_opt          # 为每个容器覆盖默认的标签 (在使用 swarm 部署时将忽略该选项)
 
        stop_grace_period     # 指定在发送了 SIGTERM 信号之后, 容器等待多少秒之后退出(默认 10s)
 
        stop_signal           # 指定停止容器发送的信号 (默认为 SIGTERM 相当于 kill PID; SIGKILL 相当于 kill -9 PID; 在使用 swarm 部署时将忽略该选项)
 
        sysctls               # 设置容器中的内核参数 (在使用 swarm 部署时将忽略该选项)
 
        ulimits               # 设置容器的 limit
 
        userns_mode           # 如果Docker守护程序配置了用户名称空间, 则禁用此服务的用户名称空间 (在使用 swarm 部署时将忽略该选项)
 
        volumes               # 定义容器和宿主机的卷映射关系, 其和 networks 一样可以位于 services 键的二级键和 compose 顶级键, 如果需要跨服务间使用则在顶级键定义, 在 services 中引用
            SHORT 语法格式示例:
                volumes:
                    - /var/lib/mysql                # 映射容器内的 /var/lib/mysql 到宿主机的一个随机目录中
                    - /opt/data:/var/lib/mysql      # 映射容器内的 /var/lib/mysql 到宿主机的 /opt/data
                    - ./cache:/tmp/cache            # 映射容器内的 /var/lib/mysql 到宿主机 compose 文件所在的位置
                    - ~/configs:/etc/configs/:ro    # 映射容器宿主机的目录到容器中去, 权限只读
                    - datavolume:/var/lib/mysql     # datavolume 为 volumes 顶级键定义的目录, 在此处直接调用
 
            LONG 语法格式示例:(v3.2 新增的语法格式)
                version: "3.2"
                services:
                    web:
                        image: nginx:alpine
                        ports:
                            - "80:80"
                        volumes:
                            - type: volume                  # mount 的类型, 必须是 bind、volume 或 tmpfs
                                source: mydata              # 宿主机目录
                                target: /data               # 容器目录
                                volume:                     # 配置额外的选项, 其 key 必须和 type 的值相同
                                    nocopy: true                # volume 额外的选项, 在创建卷时禁用从容器复制数据
                            - type: bind                    # volume 模式只指定容器路径即可, 宿主机路径随机生成; bind 需要指定容器和数据机的映射路径
                                source: ./static
                                target: /opt/app/static
                                read_only: true             # 设置文件系统为只读文件系统
                volumes:
                    mydata:                                 # 定义在 volume, 可在所有服务中调用
 
        restart               # 定义容器重启策略(在使用 swarm 部署时将忽略该选项, 在 swarm 使用 restart_policy 代替 restart)
            no                    # 禁止自动重启容器(默认)
            always                # 无论如何容器都会重启
            on-failure            # 当出现 on-failure 报错时, 容器重新启动
 
        其他选项：
            domainname, hostname, ipc, mac_address, privileged, read_only, shm_size, stdin_open, tty, user, working_dir
            上面这些选项都只接受单个值和 docker run 的对应参数类似
 
        对于值为时间的可接受的值：
            2.5s
            10s
            1m30s
            2h32m
            5h34m56s
            时间单位: us, ms, s, m， h
        对于值为大小的可接受的值：
            2b
            1024kb
            2048k
            300m
            1gb
            单位: b, k, m, g 或者 kb, mb, gb
    networks          # 定义 networks 信息
        driver                # 指定网络模式, 大多数情况下, 它 bridge 于单个主机和 overlay Swarm 上
            bridge                # Docker 默认使用 bridge 连接单个主机上的网络
            overlay               # overlay 驱动程序创建一个跨多个节点命名的网络
            host                  # 共享主机网络名称空间(等同于 docker run --net=host)
            none                  # 等同于 docker run --net=none
        driver_opts           # v3.2以上版本, 传递给驱动程序的参数, 这些参数取决于驱动程序
        attachable            # driver 为 overlay 时使用, 如果设置为 true 则除了服务之外，独立容器也可以附加到该网络; 如果独立容器连接到该网络，则它可以与其他 Docker 守护进程连接到的该网络的服务和独立容器进行通信
        ipam                  # 自定义 IPAM 配置. 这是一个具有多个属性的对象, 每个属性都是可选的
            driver                # IPAM 驱动程序, bridge 或者 default
            config                # 配置项
                subnet                # CIDR格式的子网，表示该网络的网段
        external              # 外部网络, 如果设置为 true 则 docker-compose up 不会尝试创建它, 如果它不存在则引发错误
        name                  # v3.5 以上版本, 为此网络设置名称
文件格式示例：
    version: "3"
    services:
      redis:
        image: redis:alpine
        ports:
          - "6379"
        networks:
          - frontend
        deploy:
          replicas: 2
          update_config:
            parallelism: 2
            delay: 10s
          restart_policy:
            condition: on-failure
      db:
        image: postgres:9.4
        volumes:
          - db-data:/var/lib/postgresql/data
        networks:
          - backend
        deploy:
          placement:
            constraints: [node.role == manager]
```



### 7.5 开源项目

#### 博客

>  https://docs.docker.com/compose/wordpress/ 

compose应用。=>一键启动！

1、下载项目（docker-.compose.yaml)

2、如果需要文件。Dcokerfile

3、文件准备齐全（直接一键启动项目！）

```shell
# 1、创建项目路径
mkdir my_wordpress/
cd my_wordpress/
# 2、编写docker-compose.yml文件
version: '3.3'

services:
   db:
     image: mysql:5.7
     volumes:
       - db_data:/var/lib/mysql
     restart: always
     environment:
       MYSQL_ROOT_PASSWORD: somewordpress
       MYSQL_DATABASE: wordpress
       MYSQL_USER: wordpress
       MYSQL_PASSWORD: wordpress

   wordpress:
     depends_on:
       - db
     image: wordpress:latest
     ports:
       - "8000:80"
     restart: always
     environment:
       WORDPRESS_DB_HOST: db:3306
       WORDPRESS_DB_USER: wordpress
       WORDPRESS_DB_PASSWORD: wordpress
       WORDPRESS_DB_NAME: wordpress
volumes:
    db_data: {}
```



掌握：docker基础，原理、网络、服务、集群、错误排查、日志。

后台启动：

docker-compose up -d



## 8、Docker Swarm

>  https://docs.docker.com/develop/ 

### 8.1 工作模式



分为管理节点和工作节点

raft 一致性算法

操作都是在 manager



```shell
[root@hchost ~]# docker swarm init
Swarm initialized: current node (w4ityhkbi3b6v7un65ir2v1zg) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-3cjava2g47qg1qrfpbam689q3p55gic6jon1uw9d3ltbjdrcou-eu2i4snpt0ykr0s7hf86dbebz 172.19.68.100:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.

# 移除当前节点
[root@hchost ~]# docker swarm leave -f
```



```shell
# 设置当前容器为主manager
[root@hchost ~]# docker swarm init --advertise-addr 172.19.68.100
Swarm initialized: current node (ieyuox2c9rw39zub89yfu1pb7) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-4sh98ospadbtb6n30zyhv5mlpzkovxboz29ks95omwlbg1l57r-cagajdxv7l7heuenez2eugbus 172.19.68.100:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.

```

例如，在hchost01上运行如下命令，是把hchost01作为worker加入到hchost下

```shell
docker swarm join --token SWMTKN-1-4sh98ospadbtb6n30zyhv5mlpzkovxboz29ks95omwlbg1l57r-cagajdxv7l7heuenez2eugbus 172.19.68.100:2377
```

查看当前节点的信息	

```shell
[root@hchost ~]# docker node ls
ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
ieyuox2c9rw39zub89yfu1pb7 *   hchost              Ready               Active              Leader              19.03.12

```

```shell
# 获取令牌
docker swarm join-token manager
docker swarm join-token worker

# 主节点上运行这个命令后生成令牌，其他节点运行令牌可加入主节点，成为manager或者worker
```



Reachable  是加入主节点的manager



### 8.2 Raft协议

双主双从：假设一个节点挂了！其他节点是否可以用！

Raft协议：保证大多数节点存活才可以用。只要>1，熊群至少大于3台！

实验：

将docker1机器停止。宕机！双主，另外一个主节点也不能使用了！



灰度发布：

```shell
[root@hchost ~]# docker service create -p 8888:80 --name my-nginx nginx
5k0edejorgrfip917xb61u6fd
overall progress: 1 out of 1 tasks 
1/1: running   [==================================================>] 
verify: Service converged 


# docker run 容器启动，不具备扩缩容
# docker service 服务！ 具备扩缩容，滚动更新！

# 查看服务，副本REPLICAS只有一个
[root@hchost ~]# docker service ps my-nginx
ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE           ERROR               PORTS
op6lv9ft2l9c        my-nginx.1          nginx:latest        hchost              Running             Running 4 minutes ago                       
[root@hchost ~]# docker service ls
ID                  NAME                MODE                REPLICAS            IMAGE               PORTS
5k0edejorgrf        my-nginx            replicated          1/1                 nginx:latest        *:8888->80/tcp


# 动态扩缩容
# 命令一
docker service update --replicas 3 my-nginx
# 命令二
docker service scale my-nginx=5

# 移除服务
[root@hchost ~]# docker service rm my-nginx
my-nginx
```

### 8.3 概念总结

**swarm**

集群的管理和编排。

**docker**

可以初始化一个swarm集群，其他节点可以加入。（管理、工作者）

**Node**

就是一个docker节点。多个节点就组成了一个网络集群。（管理、工作者）

**Service**

任务，可以在管理节点或者工作节点来运行。核心。！用户访问！

**Task**

容器内的命令，细节任务！





## 9、Docker Stack 

docker-compose单机部署项目！

Docker Stack部署，集群部署！

```shell
# 单机 
docker-compose up-d wordpress.yaml 
# 集群 
docker stack deploy wordpgess.yam1
```











## 10、Docker 问题汇总



### 10.1 image has dependent child images

> #### 原因

发现其实是因为TAG的问题，即有其他 image FROM 了这个 image，可以使用下面的命令列出所有在指定 image 之后创建的 image 的父 image

> #### 方案

```shell
# 先查询依赖
docker image inspect --format='{{.RepoTags}} {{.Id}} {{.Parent}}' $(docker image ls -q --filter since=XXX)    # XXX指镜像ID

# 然后根据根据TAG删除容器
docker rmi REPOSITORY:TAG
```



>  `docker system prune` 命令将删除所有停止的容器，所有悬挂的镜像和所有未使用的网络 

如果您还想删除所有未使用的卷，请传递`--volumes`标志：

```bash
docker system prune --volumes

# 删除孤立的容器
docker containers prune
```

### 10.2 已启动容器修改添加端口映射

#### 1、关闭docker服务

一定要停止docker服务，不然修改不成功

```sh
systemctl stop docker
```

#### 2、修改hostconfig.json

```javascript
vim /var/lib/docker/containers/CONTAINER ID/hostconfig.json
# 修改CONTAINER ID 为容器id
```

#### 3、修改config.v2.json



#### 4、重启 docker服务

```sh
service docker restart
```

