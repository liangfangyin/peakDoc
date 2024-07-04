# 一、概述

## 1.1 基本概念

Docker 是一个开源的应用容器引擎，基于 Go 语言 并遵从 Apache2.0 协议开源。

Docker 可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。

容器是完全使用沙箱机制，相互之间不会有任何接口（类似 iPhone 的 app），更重要的是容器性能开销极低。

## 1.2 优势

Docker 是一个用于开发，交付和运行应用程序的开放平台。Docker 使您能够将应用程序与基础架构分开，从而可以快速交付软件。借助 Docker，您可以与管理应用程序相同的方式来管理基础架构。通过利用 Docker 的方法来快速交付，测试和部署代码，您可以大大减少编写代码和在生产环境中运行代码之间的延迟。

**1、快速，一致地交付您的应用程序**

Docker 允许开发人员使用您提供的应用程序或服务的本地容器在标准化环境中工作，从而简化了开发的生命周期。

容器非常适合持续集成和持续交付（CI / CD）工作流程，请考虑以下示例方案：

- 您的开发人员在本地编写代码，并使用 Docker 容器与同事共享他们的工作。
- 他们使用 Docker 将其应用程序推送到测试环境中，并执行自动或手动测试。
- 当开发人员发现错误时，他们可以在开发环境中对其进行修复，然后将其重新部署到测试环境中，以进行测试和验证。
- 测试完成后，将修补程序推送给生产环境，就像将更新的镜像推送到生产环境一样简单。

**2、响应式部署和扩展**

Docker 是基于容器的平台，允许高度可移植的工作负载。Docker 容器可以在开发人员的本机上，数据中心的物理或虚拟机上，云服务上或混合环境中运行。

Docker 的可移植性和轻量级的特性，还可以使您轻松地完成动态管理的工作负担，并根据业务需求指示，实时扩展或拆除应用程序和服务。

**3、在同一硬件上运行更多工作负载**

Docker 轻巧快速。它为基于虚拟机管理程序的虚拟机提供了可行、经济、高效的替代方案，因此您可以利用更多的计算能力来实现业务目标。Docker 非常适合于高密度环境以及中小型部署，而您可以用更少的资源做更多的事情。

## 1.3 与传统VM特性对比

作为一种轻量级的虚拟化方式，Docker在运行应用上跟传统的虚拟机方式相比具有显著优势：

Docker 容器很快，启动和停止可以在秒级实现，这相比传统的虚拟机方式要快得多。

Docker 容器对系统资源需求很少，一台主机上可以同时运行数千个Docker容器。

Docker 通过类似Git的操作来方便用户获取、分发和更新应用镜像，指令简明，学习成本较低。

Docker 通过Dockerfile配置文件来支持灵活的自动化创建和部署机制，提高工作效率。

Docker 容器除了运行其中的应用之外，基本不消耗额外的系统资源，保证应用性能的同时，尽量减小系统开销。

Docker 利用Linux系统上的多种防护机制实现了严格可靠的隔离。从1.3版本开始，Docker引入了安全选项和镜像签名机制，极大地提高了使用Docker的安全性。

![](http://www.uml.org.cn/yunjisuan/images/2022052741.jpg#id=XGciX&originHeight=195&originWidth=579&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

## 1.4 基础架构

Docker 使用客户端-服务器 (C/S) 架构模式，使用远程API来管理和创建Docker容器。

Docker 容器通过 Docker 镜像来创建。

容器与镜像的关系类似于面向对象编程中的对象与类。

![](http://www.uml.org.cn/yunjisuan/images/2022052742.jpg#id=gkdcD&originHeight=485&originWidth=576&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

## 1.5 Docker 技术的基础

**namespace**，容器隔离的基础，保证A容器看不到B容器. 6个名空间：User，Mnt，Network，UTS，IPC，Pid

**cgroups**，容器资源统计和隔离。主要用到的cgroups子系统：cpu，blkio，device，freezer，memory

**unionfs**，典型：aufs/overlayfs，分层镜像实现的基础

## 1.6 Docker 组件

**docker Client客户端** -->向docker服务器进程发起请求，如:创建、停止、销毁容器等操作

**docker Server服务器进程** -->处理所有docker的请求，管理所有容器

**docker Registry镜像仓库** -->镜像存放的中央仓库，可看作是存放二进制的scm

## 1.7 镜像站点

[https://hub.docker.com/](https://hub.docker.com/)

# 二、安装部署

## 2.1 准备条件

目前，CentOS 仅发行版本中的内核支持 Docker。Docker 运行在 CentOS 7 上，要求系统为64位、系统内核版本为 3.10 以上。

Docker 运行在 CentOS-6.5 或更高的版本的 CentOS 上，要求系统为64位、系统内核版本2.6.32-431 或者更高版本。

## 2.2 安装 Docker

```
yum install docker -y #安装
systemctl start docker #启动
systemctl restart docker #重启
systemctl stop docker #停止
systemctl enable docker #设置开机自启动
systemctl status docker #查看docker 运行状态
docker version #查看docker 版本号信息
```

## 2.3 基本命令

### **2.3.1、设置国内镜像拉取：**

```
1.Docker中国区官方镜像
https://registry.docker-cn.com

2.网易
http://hub-mirror.c.163.com

3.ustc 
https://docker.mirrors.ustc.edu.cn

4.中国科技大学
https://docker.mirrors.ustc.edu.cn

# 创建或修改 /etc/docker/daemon.json 文件，修改为如下形式：
{
    "registry-mirrors": [
        "http://hub-mirror.c.163.com",
        "https://docker.mirrors.ustc.edu.cn",
        "https://registry.docker-cn.com"
    ]
}
#重启失效
systemctl restart docker
```

### **2.3.2、搜索镜像：**

```
docker search 镜像名称 
如：
docker search nginx
```

### **2.3.3、拉取镜像：**

```
docker pull 镜像名称
如：
docker pull nginx
```

### **2.3.4、查看本地镜像**：

```
docker images
```

### **2.3.5、运行镜像：**

```
docker run -it -d --name 要取的别名 镜像名:Tag /bin/bash 
如：
docker run -i -t --name redis001  redis:5.0.5  /bin/bash
docker run -itd --name redis002 -p 8888:6379 redis:5.0.5 /bin/bash
# 运行一个docker redis 容器 进行 端口映射 两个数据卷挂载 设置开机自启动
docker run -d -p 6379:6379 --name redis505 --restart=always  -v /var/lib/redis/data/:/data -v /var/lib/redis/conf/:/usr/local/etc/redis/redis.conf  redis:5.0.5 --requirepass "password"
```

-t 表示在新容器内指定一个伪终端或终端；

-i 表示允许我们对容器内的 (STDIN) 进行交互；

-d 表示将容器在后台运行；

-p 宿主机端口:容器端口

-v 宿主机文件存储位置:容器内文件位置

/bin/bash 。这将在容器内启动 bash shell；

### **2.3.6、删除镜像**

```
#删除一个
docker rmi -f 镜像名/镜像ID

#删除多个 其镜像ID或镜像用用空格隔开即可 
docker rmi -f 镜像名/镜像ID 镜像名/镜像ID 镜像名/镜像ID

#删除全部镜像  -a 意思为显示全部, -q 意思为只显示ID
docker rmi -f $(docker images -aq)
```

### **2.3.7、强制删除镜像**

```
docker image rm 镜像名称/镜像ID
```

### **2.3.8、保存镜像**

将我们的镜像 保存为tar 压缩文件 这样方便镜像转移和保存 ,然后 可以在任何一台安装了docker的服务器上 加载这个镜像

```
docker save 镜像名/镜像ID -o 镜像保存在哪个位置与名字
如：
docker save tomcat -o /myimg.tar
```

### **2.3.9、加载镜像**

```
docker load -i 镜像保存文件位置
如：
docker load -i  /myimg.tar
```

### **2.3.10、镜像标签**

对一个镜像进行分类或者版本迭代操作

```
docker tag 源镜像名:TAG 想要生成新的镜像名:新的TAG
如：
docker tag nginx:latest ngxin:v1.15.5
app:1.0.0 基础镜像
# 分离为开发环境
app:develop-1.0.0   
# 分离为alpha环境
app:alpha-1.0.0
```

### **2.3.11、查看正在运行容器列**

```
docker ps
```

### **2.3.12、查看所有容器**

```
包含正在运行 和已停止的
docker ps -a
```

### **2.3.13、停止容器**

```
docker stop 容器名/容器ID
如
docker stop nginx
```

### **2.3.14、进入容器**

```
方式一：
docker exec -it 容器名/容器ID /bin/bash
如
docker exec -it redis001 /bin/bash
方式二：
docker attach 容器名/容器ID
如
docker attach redis001
```

### **2.3.15、重启容器**

```
docker restart 容器ID/容器名
```

### **2.3.16、启动容器**

```
docker start 容器ID/容器名
```

### **2.3.17、kill 容器**

```
docker kill 容器ID/容器名
```

### **2.3.18、容器文件拷贝**

```
#无论容器是否开启 都可以进行拷贝
#docker cp 容器ID/名称:文件路径  要拷贝到外部的路径   |     要拷贝到外部的路径  容器ID/名称:文件路径
#从容器内 拷出
docker cp 容器ID/名称: 容器内路径  容器外路径
#从外部 拷贝文件到容器内
docker  cp 容器外路径 容器ID/名称: 容器内路径
```

### **2.3.19、查看容器日志**

```
docker logs -f --tail=要查看末尾多少行 默认all 容器ID
```

### **2.3.20、更换容器名**

```
docker rename 容器ID/容器名 新容器名
```

### **2.3.21、自己提交一个镜像**

```
docker commit -m="提交信息" -a="作者信息" 容器名/容器ID 提交后的镜像名:Tag
```

### **2.3.22、容器操作**

```
docker create # 创建一个容器但是不启动它
docker run # 创建并启动一个容器
docker stop # 停止容器运行，发送信号SIGTERM
docker start # 启动一个停止状态的容器
docker restart # 重启一个容器
docker rm # 删除一个容器
docker kill # 发送信号给容器，默认SIGKILL
docker attach # 连接(进入)到一个正在运行的容器
docker wait # 阻塞一个容器，直到容器停止运行
```

### **2.3.23、获取容器信息**

```
docker ps # 显示状态为运行（Up）的容器
docker ps -a # 显示所有容器,包括运行中（Up）的和退出的(Exited)
docker inspect # 深入容器内部获取容器所有信息
docker logs # 查看容器的日志(stdout/stderr)
docker events # 得到docker服务器的实时的事件
docker port # 显示容器的端口映射
docker top # 显示容器的进程信息
docker diff # 显示容器文件系统的前后变化
docker 命令名称  --help  -- 获取指定命令更加详细的使用方法
```

### 2.3.24、登录

```
docker login  -- 登录
```

### 2.3.25、退出

```
docker logout -- 退出
```

### 2.3.26、镜像推送

```
docker push 镜像名称 -- 用户登录后，将镜像推送到docker hub中
```

### **2.3.27、导出容器**

```
docker export 名称 > 导出包.tar  -- 导出容器
```

### **2.3.28、执行**

```
docker exec # 在容器里执行一个命令，可以执行bash进入交互式
```

## 2.4 部署应用

### 2.4.1、镜像推送到远程服务器

1.本地镜像打包

```
docker build -t 镜像名称 .
如：
docker build -t yqapi .
```

此时本地会生成一个 **yqapi** 包

2.登录阿里云Docker Registry

```
docker login --username=用户名 registry.cn-hangzhou.aliyuncs.com  --password=密码
```

用于登录的用户名为阿里云账号全名，密码为开通服务时设置的密码。

3.打标签包

```
docker tag  镜像id registry.cn-hangzhou.aliyuncs.com/镜像名称:版本号
如：
docker tag  8bb5e6b28eca registry.cn-hangzhou.aliyuncs.com/sovell-cloud/yqapi:1.0.7
```

**8bb5e6b28eca**：步骤1中本地打包的ydapi的镜像id

4、推送镜像

```
docker push registry.cn-hangzhou.aliyuncs.com/镜像名称:版本号
如：
docker push registry.cn-hangzhou.aliyuncs.com/sovell-cloud/yqapi:1.0.7
```

### 2.4.3、Net Core

1、项目中新增dockerfile文件

```
# 环境运行时基础镜像
FROM mcr.microsoft.com/dotnet/aspnet:5.0 AS base
#将当前目录内容 copy到镜像目录
COPY .  /app
#设置工作目录
WORKDIR /app 
EXPOSE 80
EXPOSE 443

#环境变量  变量名="变量值"
ENV databasic="数据库连接字符串"
ENV redisconnection="127.0.0.1:6379,password="

## 设置所属时区
ENV TZ=Asia/Shanghai
## 创建本地和容器的连接
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
#运行镜像入口命令和可执行文件名称
ENTRYPOINT ["dotnet", "yqApi.dll"]
```

配置好后，发布版本项目

2、发布版本

进入发布的文件夹目录

```
#docker build -t <镜像名称> <目录> .为当前目录
docker build -t yqApi .
```

3、运行镜像

```
#docker run --name=<容器名称>  -d<后台运行> -p <外部暴露端口>:<容器端口> <关联镜像>
docker run --name=yqApi -d -p 8080:80 yqApi:latest
```

### 2.4.4、Nginx部署

1、安装

```
docker pull nginx
```

2、运行容器

```
docker run --name nginx-atlascca3 --privileged=true -p 80:80 -v /home/nginx/nginx.conf:/etc/nginx/nginx.conf -v /home/nginx/vhost:/home/nginx/vhost -v /home/nginx/logs/nginx_error.log:/home/nginx/logs/nginx_error -v /home/nginx/www:/home/nginx/www -d nginx
```

-d: 后台运行容器，并返回容器UUID**（常用）**

-i: 以交互模式运行容器，通常与 -t 同时使用

-t: 为容器重新分配一个伪输入终端，通常与 -i 同时使用

-v, –volume=[] 给容器挂载存储卷，挂载到容器的某个目录**（常用）**

–privileged=false 指定容器是否为特权容器，特权容器拥有所有的capabilities

–name=”” 指定容器名字（如mynginx），后续可以通过这个容器名字进行容器管理**（常用）**

### 2.4.5、Node.js

1、安装

```
docker pull node:latest
```

2、运行容器

```
docker run -itd --name node-test node
```

3、安装成功

```
docker exec -it node-test /bin/bash
```

### 2.4.6、MySQL

1、安装

```
docker pull mysql:latest
```

2、运行容器

```
docker run -itd --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql
```

	-p 3306:3306 ：映射容器服务的 3306 端口到宿主机的 3306 端口，外部主机可以直接通过 宿主机ip:3306 访问到 MySQL 的服务。
	MYSQL_ROOT_PASSWORD=123456：设置 MySQL 服务 root 用户的密码。
3、进入容器

```
docker exec -it mysql bash
```

4、登录MySQL

```
mysql -u root -p 123456
# 重命名root密码（可选）
ALTER USER 'root'@'localhost' IDENTIFIED BY '密码';
```

5、添加远程登录用户

```
CREATE USER '新用户' IDENTIFIED WITH mysql_native_password BY '新用户密码';
# 设置远程可访问
GRANT ALL PRIVILEGES ON *.* TO '新用户';
```

### 2.4.9、Redis

1、安装

```
 docker pull redis:latest
```

2、运行容器

```
docker run -itd --name redis-test -p 6379:6379 redis
```

3、安装成功

```
docker exec -it redis-test /bin/bash
```

4、进入操作

```
redis-cli
```

### 2.4.10、Mongo

1、安装

```
 docker pull mongo:latest
```

2、运行容器

```
docker run -itd --name mongo -p 27017:27017 mongo --auth
```

-p 27017:27017 ：映射容器服务的 27017 端口到宿主机的 27017 端口。外部可以直接通过 宿主机 ip:27017 访问到 mongo 的服务。
--auth：需要密码才能访问容器服务。
3、进入操作

```
docker exec -it mongo mongo admin
# 创建一个名为 admin，密码为 123456 的用户。
db.createUser({ user:'admin',pwd:'123456',roles:[ { role:'userAdminAnyDatabase', db: 'admin'},"readWriteAnyDatabase"]});
# 尝试使用上面创建的用户信息进行连接。
 db.auth('admin', '123456')
```

### 2.4.11、ActiveMQ

1、安装

```
docker pull webcenter/activemq
```

2、运行容器

```
 docker run -d --name activemq -p 61616:61616 -p 8161:8161 webcenter/activemq
```

3、进入操作

```
docker exec -it activemq /bin/bash
```

4、访问地址：[http://localhost:8161](http://localhost:8161)   默认用户：admin 　　默认密码：admin

### 2.4.12、Rancher

1、安装Rancher

```
docker run --privileged -d --restart=unless-stopped -p 80:80 -p 443:443  rancher/rancher
```

2、重置密码：

```
docker exec -ti  容器ID  reset-password
```

3、查看容器运行状态

```
docker ps
```

# 三、Dockerfile

Dockerfile的指令是忽略大小写的，建议使用大写，使用 # 作为注释，每一行只支持一条指令，每条指令可以携带多个参数。

Dockerfile的指令根据作用可以分为两种，构建指令和设置指令。

**构建指令**：用于构建 image，其指定的操作不会在运行image的容器上执行；

**设置指令**：用于设置 image 的属性，其指定的操作将在运行image的容器中执行。

- **FROM（指定基础image）**

构建指令，必须指定且需要在 Dockerfile 其他指令的前面。后续的指令都依赖于该指令指定的image。FROM 指令指定的基础 image 可以是官方远程仓库中的，也可以位于本地仓库。

该指令有两种格式：

```
FROM <image>       #指定基础image为该image的最后修改的版本FROM <image>:<tag> #指定基础image为该image的一个tag版本。
```

- **MAINTAINER（用来指定镜像创建者信息）**

构建指令，用于将image的制作者相关的信息写入到image中。当我们对该image执行docker inspect命令时，输出中有相应的字段记录该信息。

```
MAINTAINER <name>
```

- **RUN（安装软件用）**

构建指令，RUN可以运行任何被基础image支持的命令。如基础image选择了ubuntu，那么软件管理部分只能使用ubuntu的命令。

```
RUN <command> (the command is run in a shell - `/bin/sh -c`)RUN ["executable", "param1", "param2" ... ]  (exec form)
```

- **CMD（设置container启动时执行的操作）**

设置指令，用于container启动时指定的操作。该操作可以是执行自定义脚本，也可以是执行系统命令。该指令只能在文件中存在一次，如果有多个，则只执行最后一条。

```
CMD ["executable","param1","param2"] (like an exec, this is the preferred form)CMD command param1 param2 (as a shell)
```

ENTRYPOINT 指定的是一个可执行的脚本或者程序的路径，该指定的脚本或者程序将会以 param1 和param2作为参数执行。

所以如果CMD指令使用上面的形式，那么Dockerfile中必须要有配套的ENTRYPOINT。当Dockerfile指定了ENTRYPOINT，那么使用下面的格式：

```
CMD ["param1","param2"] (as default parameters to ENTRYPOINT)
```

- **ENTRYPOINT（设置container启动时执行的操作）**

设置指令，指定容器启动时执行的命令，可以多次设置，但是只有最后一个有效。

```
ENTRYPOINT ["executable", "param1", "param2"] (like an exec, the preferred form)ENTRYPOINT command param1 param2 (as a shell)
```

该指令的使用分为两种情况，一种是独自使用，另一种和CMD指令配合使用。

当独自使用时，如果你还使用了CMD命令且CMD是一个完整的可执行的命令，那么CMD指令和ENTRYPOINT会互相覆盖只有最后一个CMD或者ENTRYPOINT有效。

```
# CMD指令将不会被执行，只有ENTRYPOINT指令被执行CMD echo “Hello, World!”ENTRYPOINT ls -l
```

另一种用法和CMD指令配合使用来指定ENTRYPOINT的默认参数，这时CMD指令不是一个完整的可执行命令，仅仅是参数部分；ENTRYPOINT指令只能使用JSON方式指定执行命令，而不能指定参数。

```
FROM ubuntuCMD ["-l"]ENTRYPOINT ["/usr/bin/ls"]
```

- **USER（设置container容器的用户）**

设置指令，设置启动容器的用户，默认是root用户

```
# 指定memcached的运行用户ENTRYPOINT ["memcached"]USER daemon或ENTRYPOINT ["memcached", "-u", "daemon"]
```

- **EXPOSE（指定容器需要映射到宿主机器的端口）**

设置指令，该指令会将容器中的端口映射成宿主机器中的某个端口。当你需要访问容器的时候，可以不是用容器的IP地址而是使用宿主机器的IP地址和映射后的端口。

要完成整个操作需要两个步骤，首先在Dockerfile使用EXPOSE设置需要映射的容器端口，然后在运行容器的时候指定-p选项加上EXPOSE设置的端口，这样EXPOSE设置的端口号会被随机映射成宿主机器中的一个端口号。

也可以指定需要映射到宿主机器的那个端口，这时要确保宿主机器上的端口号没有被使用。EXPOSE指令可以一次设置多个端口号，相应的运行容器的时候，可以配套的多次使用-p选项。

```
# 映射一个端口EXPOSE port1# 相应的运行容器使用的命令  (主机(宿主)端口:容器端口)docker run -p port1 image
# 映射多个端口EXPOSE port1 port2 port3# 相应的运行容器使用的命令docker run -p port1 -p port2 -p port3 image# 还可以指定需要映射到宿主机器上的某个端口号docker run -p host_port1:port1 -p host_port2:port2 -p host_port3:port3 image
```

端口映射是 Docker 比较重要的一个功能，原因在于我们每次运行容器的时候容器的IP地址不能指定而是在桥接网卡的地址范围内随机生成的。

宿主机器的IP地址是固定的，我们可以将容器的端口的映射到宿主机器上的一个端口，免去每次访问容器中的某个服务时都要查看容器的IP的地址。

对于一个运行的容器，可以使用docker port加上容器中需要映射的端口和容器的ID来查看该端口号在宿主机器上的映射端口。

- **ENV（用于设置环境变量）**

构建指令，在image中设置一个环境变量。

```
ENV <key> <value>
```

设置了后，后续的RUN命令都可以使用，container启动后，可以通过docker inspect查看这个环境变量，也可以通过在docker run —env key=value时设置或修改环境变量。

假如你安装了JAVA程序，需要设置JAVA_HOME，那么可以在Dockerfile中这样写：

```
ENV JAVA_HOME /path/to/java/dirent
```

- **ADD（从src复制文件到container的dest路径）**

构建指令，所有拷贝到container中的文件和文件夹权限为0755，uid和gid为0；

如果是一个目录，那么会将该目录下的所有文件添加到container中，不包括目录；如果文件是可识别的压缩格式，则docker会帮忙解压缩（注意压缩格式）；

如果`<src>`是文件且`<dest>`中不使用斜杠结束，则会将`<dest>`视为文件，`<src>`的内容会写入`<dest>`；

如果`<src>`是文件且`<dest>`中使用斜杠结束，则会`<src>`文件拷贝到`<dest>`目录下。

```
ADD <src> <dest>
```

是相对被构建的源目录的相对路径，可以是文件或目录的路径，也可以是一个远程的文件url;

是container中的绝对路径

- **VOLUME（指定挂载点)**

设置指令，使容器中的一个目录具有持久化存储数据的功能，该目录可以被容器本身使用，也可以共享给其他容器使用。

我们知道容器使用的是AUFS，这种文件系统不能持久化数据，当容器关闭后，所有的更改都会丢失。当容器中的应用有持久化数据的需求时可以在Dockerfile中使用该指令。

```
FROM baseVOLUME ["/tmp/data"]
```

- **WORKDIR（切换目录）**

设置指令，可以多次切换(相当于cd命令)，对RUN,CMD,ENTRYPOINT生效。

```
# 在 /p1/p2 下执行 vim a.txtWORKDIR /p1 WORKDIR p2 RUN vim a.txt
```

**案例：**

```
#See https://aka.ms/containerfastmode to understand how Visual Studio uses this Dockerfile to build your images for faster debugging.
# 环境运行时基础镜像
FROM mcr.microsoft.com/dotnet/aspnet:5.0 AS base
#将当前目录内容 copy到镜像目录
COPY .  /app
#设置工作目录
WORKDIR /app 
EXPOSE 80
EXPOSE 443

## 设置所属时区
ENV TZ=Asia/Shanghai
## 创建本地和容器的连接
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# 环境变量
ENV iscloud="1"
ENV issingleshop="1"

#运行镜像入口命令和可执行文件名称
ENTRYPOINT ["dotnet", "yqApi.dll"]
```

# 四、网络

docker提供几种网络，它决定容器之间和外界和容器之间如何去相互通信。

```
docker network ls        #查看网络
```

### 4.1 bridge桥接

网络除非创建容器的时候指定网络，不然容器就会默认的使用桥接网络。属于这个网络的容器之间可以相互通信，不过外界想要访问到这个网络的容器呢，需使用桥接网络，有点像主机和容器之间的一座桥，对容器有一点隔离作用。

实际是在iptables做了DNAT规则，实现端口转发功能。可以使用iptables -t nat -vnL查看。

### 4.3 host 主机网络

如果启动容器的时候使用host模式，那么这个容器将不会获得一个独立的Network Namespace，而是和宿主机共用一个 Network Namespace。容器将不会虚拟出自己的网卡，配置自己的IP等，而是使用宿主机的IP和端口。

但是，容器的其他方面，如文件系统、进程列表等还是和宿主机隔离的。只用这种网络的容器会使用主机的网络，这种网络对外界是完全开放的，能够访问到主机，就能访问到容器。

### 4.4 使用 none 模式

使用none模式Docker容器拥有自己的Network Namespace，但是，并不为Docker容器进行任何网络配置。也就是说，这个Docker容器没有网卡、IP、路由等信息。需要我们自己为Docker容器添加网卡、配置IP等。使用此种网络的容器会完全隔离。
