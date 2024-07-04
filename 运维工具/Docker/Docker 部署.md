查看已启动的服务：systemctl list-units --type=service
查看是否设置开机启动：systemctl list-unit-files | grep enable
设置开机启动：systemctl enable docker.service
关闭开机启动：systemctl disable docker.service

### 卸载旧版本
$ sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine

**设置仓库**
安装所需的软件包。yum-utils 提供了 yum-config-manager ，并且 device mapper 存储驱动程序需要 device-mapper-persistent-data 和 lvm2。
$ sudo yum install -y yum-utils

使用以下命令来设置稳定的仓库。
## 使用官方源地址（比较慢）
$ **sudo** yum-config-manager \
 --add-repo \
https:**//**download.docker.com**/**linux**/**centos**/**docker-ce.repo
可以选择国内的一些源地址：
## 阿里云
$ **sudo** yum-config-manager \
 --add-repo \
https:**//**mirrors.aliyun.com**/**docker-ce**/**linux**/**centos**/**docker-ce.repo

## 清华大学源
$ **sudo** yum-config-manager \
 --add-repo \
https:**//**mirrors.tuna.tsinghua.edu.cn**/**docker-ce**/**linux**/**centos**/**docker-ce.repo

### 安装 Docker Engine-Community
安装最新版本的 Docker Engine-Community 和 containerd，或者转到下一步安装特定版本：
$ sudo yum install docker-ce docker-ce-cli containerd.io docker-compose-plugin


启动 Docker。
$ sudo systemctl start docker
通过运行 hello-world 镜像来验证是否正确安装了 Docker Engine-Community 。
$ sudo docker run hello-world





**docker 安装 jenkins**
要在Docker中安装Jenkins，您可以使用官方的Jenkins Docker镜像。以下是安装和运行Jenkins的步骤：

1. 拉取官方的Jenkins Docker镜像：
```bash
docker pull jenkins/jenkins:lts
```

1. 创建一个Docker容器并运行：
```bash
docker run --name jenkins -p 8080:8080 -p 50000:50000 -v jenkins_home:/var/jenkins_home -d jenkins/jenkins:lts
```
在这个命令中：

- --name jenkins 给容器指定一个名字。
- -p 8080:8080 映射容器内的8080端口到宿主机的8080端口。
- -p 50000:50000 映射Jenkins代理端口。
- -v jenkins_home:/var/jenkins_home 持久化Jenkins数据目录。
- -d 表示以守护态运行容器。
- jenkins/jenkins:lts 指定使用Jenkins的长期支持版镜像。


systemctl start docker   启动docker

systemctl restart docker.service  重启docker

docker ps  -- 查看正在运行的文档

docker logs 名称  -- 查看容器内标准输出

docker stop 名称  -- 停止容器

docker run ubuntu:15.10 /bin/echo "Hello world"   -- 启动运行容器

docker 命令名称  --help  -- 获取指定命令更加详细的使用方法

docker pull 名称  -- 如果本地没有镜像，从服务器拉取

docker start 名称  -- 运行已经停止的容器

docker export 名称 > 导出包.tar  -- 导出容器

cat docker/ubuntu.tar | docker import - test/ubuntu:v1  -- 本地导入容器

docker import [http://example.com/exampleimage.tgz](http://example.com/exampleimage.tgz) example/imagerepo  -- 指定url导入容器

docker rm -f 名称  -- 删除容器

docker port 名称  -- 查看程序运行端口

docker images -- 列出镜像列表

docker search 镜像名称  -- 查找镜像

docker rmi 镜像名称 -- 删除镜像

docker commit -m="描述信息" -a="镜像作者" 容器id  镜像名称

docker login  -- 登录

docker logout -- 退出

docker push 镜像名称 -- 用户登录后，将镜像推送到docker hub中






---------------------------------Net Core-------------------------------------------------
运行一个web
1、安装：docker pull training/webapp  # 载入镜像
2、构建镜像：docker build -t mytestapi .
3、运行镜像：docker run --name=mytestapi -p 80:80 mytestapi  


---------------------------------Nginx-------------------------------------------------
1、安装：docker pull nginx
2、运行容器：docker run --name nginx-test -p 8080:80 -d nginx
	--name nginx-test：容器名称。
	-p 8080:80： 端口进行映射，将本地 8080 端口映射到容器内部的 80 端口。
	-d nginx： 设置容器在在后台一直运行。


---------------------------------Node.js-------------------------------------------------
1、安装： docker pull node:latest
2、运行容器：docker run -itd --name node-test node
3、安装成功：docker exec -it node-test /bin/bash


---------------------------------MySQL-------------------------------------------------
1、安装：docker pull mysql:latest
2、运行容器：docker run -itd --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql
	-p 3306:3306 ：映射容器服务的 3306 端口到宿主机的 3306 端口，外部主机可以直接通过 宿主机ip:3306 访问到 MySQL 的服务。
	MYSQL_ROOT_PASSWORD=123456：设置 MySQL 服务 root 用户的密码。
3、进入容器：docker exec -it mysql bash
4、登录MySQL：mysql -u root -p
	          ALTER USER 'root'@'localhost' IDENTIFIED BY '123456789';
5、添加远程登录用户：CREATE USER 'liangfy' IDENTIFIED WITH mysql_native_password BY '123456789';
		  GRANT ALL PRIVILEGES ON *.* TO 'liangfy';


---------------------------------Redis-------------------------------------------------
1、安装： docker pull redis:latest
2、运行容器：docker run -itd --name redis-test -p 6379:6379 redis
3、安装成功：docker exec -it redis-test /bin/bash
4、进入操作：redis-cli


---------------------------------Mongo-------------------------------------------------
1、安装： docker pull mongo:latest
2、运行容器：docker run -itd --name mongo -p 27017:27017 mongo --auth
	     -p 27017:27017 ：映射容器服务的 27017 端口到宿主机的 27017 端口。外部可以直接通过 宿主机 ip:27017 访问到 mongo 的服务。
	     --auth：需要密码才能访问容器服务。
3、进入操作：docker exec -it mongo mongo admin
	     创建一个名为 admin，密码为 123456 的用户。
	     db.createUser({ user:'admin',pwd:'123456',roles:[ { role:'userAdminAnyDatabase', db: 'admin'},"readWriteAnyDatabase"]});
                     尝试使用上面创建的用户信息进行连接。
	     db.auth('admin', '123456')     




---------------------------------ActiveMQ-------------------------------------------------
1、安装：   docker pull webcenter/activemq
2、运行容器： docker run -d --name activemq -p 61616:61616 -p 8161:8161 webcenter/activemq
3、进入操作： docker exec -it activemq /bin/bash
4、开启防火墙端口
	      firewall-cmd --zone=public --add-port=61616/tcp --permanent
	      firewall-cmd --zone=public --add-port=8161/tcp --permanent
	      firewall-cmd --reload

5、访问地址：[http://localhost:8161](http://localhost:8161)   默认用户：admin 　　默认密码：admin





































  
