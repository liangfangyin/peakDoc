===========================第一步：安装Redis================================
1、新建文件夹
mkdir  /usr/local/redis

2、切换目录
cd /usr/local/redis

 
3、设置权限
sudo chmod -R 777 /usr/local/redis

4、下载安装包
wget [https://download.redis.io/releases/redis-6.2.2.tar.gz](https://download.redis.io/releases/redis-6.2.2.tar.gz) --no-check-certificate

5、解压压缩包
tar xzf redis-6.2.2.tar.gz

6、设置权限
sudo chmod -R 777 /usr/local/redis/redis-6.2.2

7、进入目录：cd /usr/local/redis/redis-6.2.2

8、编译
make

9、编译安装
make install PREFIX=/usr/local/redis/redis-6.2.2

10、copy配置文件
cp -r redis.conf /usr/local/redis/redis-6.2.2/bin/

11、设置权限
sudo chmod -R 777  /usr/local/redis/redis-6.2.2/bin/

12、进入目录
cd /usr/local/redis/redis-6.2.2/bin

13、开启服务(如果出现redis服务图像代表成功 按下Crtl+C 退出)
./redis-server ./redis.conf

14、配置外网访问：
cd /usr/local/redis/redis-6.2.2/bin
打开配置文件  redis.conf
14.1、注释：bind 127.0.0.1
14.2、设置后台服务运行：daemonize yes

===========================第二步：设置启动服务================================
1、创建启动脚本文件
vim /etc/init.d/redis

2、脚本内容
===========================内容start==================================
#!/bin/sh
#chkconfig: 2345 80 90
#description:auto_run
REDISPORT=6379
#注意自己安装的redis根目录
REDISPATH=/usr/local/redis/redis-6.2.2/bin
EXEC=${REDISPATH}/redis-server
CLIEXEC=${REDISPATH}/redis-cli
PIDFILE=/var/run/redis_${REDISPORT}.pid
CONF="${REDISPATH}/redis.conf"
case "$1" in
  start)
    if [ -f $PIDFILE ]
    then
        echo "$PIDFILE exists, process is already running or crashed"
    else
        echo "Starting Redis server..."
        $EXEC $CONF
    fi
    ;;
  stop)
    if [ ! -f $PIDFILE ]
    then
        echo "$PIDFILE does not exist, process is not running"
    else
        PID=$(cat $PIDFILE)
        echo "Stopping ..."
        $CLIEXEC -p $REDISPORT shutdown
        while [ -x /proc/${PID} ]
        do
          echo "Waiting for Redis to shutdown ..."
          sleep 1
        done
        echo "Redis stopped"
    fi
    ;;
  *)
    echo "Please use start or stop as first argument"
    ;;
esac
===========================内容end==================================
按下ESC按键退出  输入:wq保存退出

3、设置文件redis的权限，让Linux可以执行
chmod 755 /etc/init.d/redis

4、启动Redis服务
/etc/init.d/redis start  

5、设置开机自启动
chkconfig redis on



===========================第三步：设置防火墙================================
1、查看防火墙状态
systemctl status firewalld

2、启动防火墙
systemctl start firewalld

3、开放指定端口
firewall-cmd --zone=public --add-port=6379/tcp --permanent

4、重启防火墙
firewall-cmd --reload


