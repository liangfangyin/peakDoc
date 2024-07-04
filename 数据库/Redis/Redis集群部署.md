
**1、创建文件夹**
mkdir  /home/redis-cluster

2**、切换目录**
cd  /home/redis-cluster

**3、新建redis-cluster.conf文件**
vim  redis-cluster.conf

port ${PORT}
protected-mode no
cluster-enabled yes
cluster-config-file nodes.conf
cluster-node-timeout 5000
cluster-announce-ip 112.65.xx.xxx //外网ip
cluster-announce-port ${PORT}
cluster-announce-bus-port 1${PORT}
appendonly yes
masterauth mypassword  //主服务器验证密码（可选）
requirepass mypassword  //连接密码（可选）


4**、创建配置文件，实行三主三从**
for port in `seq 6111 6116`; do \
  mkdir -p ./${port}/conf \
  && PORT=${port} envsubst < ./redis-cluster.conf > ./${port}/conf/redis.conf \
  && mkdir -p ./${port}/data; \
done


**5、创建容器运行**
  for port in `seq 6111 6116`; do \
  docker run -d -it  \
  -v /home/redis-cluster/${port}/conf/redis.conf:/usr/local/etc/redis/redis.conf \
  -v /home/redis-cluster/${port}/data:/data \
  --restart always --name redis-${port} --net host \
  redis:latest redis-server /usr/local/etc/redis/redis.conf; \
done


**6、搭建集群**
方式一：
docker run -i --rm -p 6379 --net host redis:latest \
   redis-cli --cluster create \
   $(for port in `seq 6111 6116`; do \
     echo -n "$(docker inspect --format '112.65.xx.xx' "redis-${port}")":${port} ' ' ; \
     done) $(echo "--cluster-replicas 1 -a 密码")
若一直停留在Waiting for the cluster to join...,请检查6111–6116,16111–16116端口防火墙是否均已开通，切勿忘记开通16111--16116端口


方式二：
	 redis-cli --cluster create 112.65.xx.xxx:6111 \
	 112.65.xx.xxx:6112 112.65.xx.xxx:6113 \
	 112.65.xx.xxx:6114 112.65.xx.xxx:6115 \
	 112.65.xx.xxx:6116 --cluster-replicas 1 -a mypassword


**7、访问集群**
进入容器：
docker exec -it redis-6111 /bin/bash

登录redis：
redis-cli -c -p 6111 -a 密码









