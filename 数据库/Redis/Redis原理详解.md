# Redis安装

## 1、Redis配置

```
===========================第一步：安装Redis================================
1、新建文件夹
mkdir  /usr/local/redis

2、切换目录
cd /usr/local/redis

 
3、设置权限
sudo chmod -R 777 /usr/local/redis

4、下载安装包
wget https://download.redis.io/releases/redis-6.2.2.tar.gz --no-check-certificate

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
```

## 2、Redis配置文件

redis目录下，配置文件redis.conf( 或redis.windows.conf )，Redis很多功能配置都在此文件完成。

可通过命令查看打开配置文件：

```
vim /etc/redis/redis.conf
```

```python
# Redis configuration file example

# Note on units: when memory size is needed, it is possible to specify
# it in the usual form of 1k 5GB 4M and so forth:
#
# 1k => 1000 bytes
# 1kb => 1024 bytes
# 1m => 1000000 bytes
# 1mb => 1024*1024 bytes
# 1g => 1000000000 bytes
# 1gb => 1024*1024*1024 bytes
#
# units are case insensitive so 1GB 1Gb 1gB are all the same.
# 制定单位，通常k,m,g,gb,单位不区分大小写

################################## INCLUDES ###################################

# Include one or more other config files here.  This is useful if you
# have a standard template that goes to all Redis servers but also need
# to customize a few per-server settings.  Include files can include
# other files, so use this wisely.
#
# Notice option "include" won't be rewritten by command "CONFIG REWRITE"
# from admin or Redis Sentinel. Since Redis always uses the last processed
# line as value of a configuration directive, you'd better put includes
# at the beginning of this file to avoid overwriting config change at runtime.
#
# If instead you are interested in using includes to override configuration
# options, it is better to use include as the last line.
#
# include .\path\to\local.conf
# include c:\path\to\other.conf
# 当存在多个配置文件时候，使用此配置


################################## NETWORK #####################################

# By default, if no "bind" configuration directive is specified, Redis listens
# for connections from all the network interfaces available on the server.
# It is possible to listen to just one or multiple selected interfaces using
# the "bind" configuration directive, followed by one or more IP addresses.
#
# Examples:
#
# bind 192.168.1.100 10.0.0.1
# bind 127.0.0.1 ::1
#
# ~~~ WARNING ~~~ If the computer running Redis is directly exposed to the
# internet, binding to all the interfaces is dangerous and will expose the
# instance to everybody on the internet. So by default we uncomment the
# following bind directive, that will force Redis to listen only into
# the IPv4 lookback interface address (this means Redis will be able to
# accept connections only from clients running into the same computer it
# is running).
#
# IF YOU ARE SURE YOU WANT YOUR INSTANCE TO LISTEN TO ALL THE INTERFACES
# JUST COMMENT THE FOLLOWING LINE.
# ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
bind 127.0.0.1

# Protected mode is a layer of security protection, in order to avoid that
# Redis instances left open on the internet are accessed and exploited.
#
# When protected mode is on and if:
#
# 1) The server is not binding explicitly to a set of addresses using the
#    "bind" directive.
# 2) No password is configured.
#
# The server only accepts connections from clients connecting from the
# IPv4 and IPv6 loopback addresses 127.0.0.1 and ::1, and from Unix domain
# sockets.
#
# By default protected mode is enabled. You should disable it only if
# you are sure you want clients from other hosts to connect to Redis
# even if no authentication is configured, nor a specific set of interfaces
# are explicitly listed using the "bind" directive.
protected-mode yes

# Accept connections on the specified port, default is 6379 (IANA #815344).
# If port 0 is specified Redis will not listen on a TCP socket.
port 6379

# TCP listen() backlog.
#
# In high requests-per-second environments you need an high backlog in order
# to avoid slow clients connections issues. Note that the Linux kernel
# will silently truncate it to the value of /proc/sys/net/core/somaxconn so
# make sure to raise both the value of somaxconn and tcp_max_syn_backlog
# in order to get the desired effect.
tcp-backlog 511

# Unix socket.
#
# Specify the path for the Unix socket that will be used to listen for
# incoming connections. There is no default, so Redis will not listen
# on a unix socket when not specified.
#
# unixsocket /tmp/redis.sock
# unixsocketperm 700

# Close the connection after a client is idle for N seconds (0 to disable)
timeout 0

# TCP keepalive.
#
# If non-zero, use SO_KEEPALIVE to send TCP ACKs to clients in absence
# of communication. This is useful for two reasons:
#
# 1) Detect dead peers.
# 2) Take the connection alive from the point of view of network
#    equipment in the middle.
#
# On Linux, the specified value (in seconds) is the period used to send ACKs.
# Note that to close the connection the double of the time is needed.
# On other kernels the period depends on the kernel configuration.
#
# A reasonable value for this option is 60 seconds.
tcp-keepalive 0
# ①、bind:绑定redis服务器网卡IP，默认为127.0.0.1,即本地回环地址。这样的话，访问redis服务只能通过本机的客户端连接，而无法通过远程连接。如果bind选项为空的话，那会接受所有来自于可用网络接口的连接。
# ②、port：指定redis运行的端口，默认是6379。由于Redis是单线程模型，因此单机开多个Redis进程的时候会修改端口。
# ③、timeout：设置客户端连接时的超时时间，单位为秒。当客户端在这段时间内没有发出任何指令，那么关闭该连接。默认值为0，表示不关闭。
# ④、tcp-keepalive ：单位是秒，表示将周期性的使用SO_KEEPALIVE检测客户端是否还处于健康状态，避免服务器一直阻塞，官方给出的建议值是300s，如果设置为0，则不会周期性的检测。

################################# GENERAL #####################################

# By default Redis does not run as a daemon. Use 'yes' if you need it.
# Note that Redis will write a pid file in /var/run/redis.pid when daemonized.
# NOT SUPPORTED ON WINDOWS daemonize no

# If you run Redis from upstart or systemd, Redis can interact with your
# supervision tree. Options:
#   supervised no      - no supervision interaction
#   supervised upstart - signal upstart by putting Redis into SIGSTOP mode
#   supervised systemd - signal systemd by writing READY=1 to $NOTIFY_SOCKET
#   supervised auto    - detect upstart or systemd method based on
#                        UPSTART_JOB or NOTIFY_SOCKET environment variables
# Note: these supervision methods only signal "process is ready."
#       They do not enable continuous liveness pings back to your supervisor.
# NOT SUPPORTED ON WINDOWS supervised no

# If a pid file is specified, Redis writes it where specified at startup
# and removes it at exit.
#
# When the server runs non daemonized, no pid file is created if none is
# specified in the configuration. When the server is daemonized, the pid file
# is used even if not specified, defaulting to "/var/run/redis.pid".
#
# Creating a pid file is best effort: if Redis is not able to create it
# nothing bad happens, the server will start and run normally.
# NOT SUPPORTED ON WINDOWS pidfile /var/run/redis.pid

# Specify the server verbosity level.
# This can be one of:
# debug (a lot of information, useful for development/testing)
# verbose (many rarely useful info, but not a mess like the debug level)
# notice (moderately verbose, what you want in production probably)
# warning (only very important / critical messages are logged)
loglevel notice

# Specify the log file name. Also 'stdout' can be used to force
# Redis to log on the standard output.
logfile ""

# To enable logging to the Windows EventLog, just set 'syslog-enabled' to
# yes, and optionally update the other syslog parameters to suit your needs.
# If Redis is installed and launched as a Windows Service, this will
# automatically be enabled.
# syslog-enabled no

# Specify the source name of the events in the Windows Application log.
# syslog-ident redis

# Set the number of databases. The default database is DB 0, you can select
# a different one on a per-connection basis using SELECT <dbid> where
# dbid is a number between 0 and 'databases'-1
databases 16
# ①、daemonize:设置为yes表示指定Redis以守护进程的方式启动（后台启动）。默认值为 no
# ②、pidfile:配置PID文件路径，当redis作为守护进程运行的时候，它会把 pid 默认写到 /var/redis/run/redis_6379.pid 文件里面
# ③、loglevel ：定义日志级别。默认值为notice，有如下4种取值：
#              debug（记录大量日志信息，适用于开发、测试阶段）
#              verbose（较多日志信息）
#              notice（适量日志信息，使用于生产环境）
#              warning（仅有部分重要、关键信息才会被记录）
# ④、logfile ：配置log文件地址,默认打印在命令行终端的窗口上
# ⑤、databases：设置数据库的数目。默认的数据库是DB 0 ，可以在每个连接上使用select  <dbid> 命令选择一个不同的数据库，dbid是一个介于0到databases - 1 之间的数值。默认值是 16，也就是说默认Redis有16个数据库。

################################ SNAPSHOTTING  ################################
#
# Save the DB on disk:
#
#   save <seconds> <changes>
#
#   Will save the DB if both the given number of seconds and the given
#   number of write operations against the DB occurred.
#
#   In the example below the behaviour will be to save:
#   after 900 sec (15 min) if at least 1 key changed
#   after 300 sec (5 min) if at least 10 keys changed
#   after 60 sec if at least 10000 keys changed
#
#   Note: you can disable saving completely by commenting out all "save" lines.
#
#   It is also possible to remove all the previously configured save
#   points by adding a save directive with a single empty string argument
#   like in the following example:
#
#   save ""

save 900 1
save 300 10
save 60 10000

# By default Redis will stop accepting writes if RDB snapshots are enabled
# (at least one save point) and the latest background save failed.
# This will make the user aware (in a hard way) that data is not persisting
# on disk properly, otherwise chances are that no one will notice and some
# disaster will happen.
#
# If the background saving process will start working again Redis will
# automatically allow writes again.
#
# However if you have setup your proper monitoring of the Redis server
# and persistence, you may want to disable this feature so that Redis will
# continue to work as usual even if there are problems with disk,
# permissions, and so forth.
stop-writes-on-bgsave-error yes

# Compress string objects using LZF when dump .rdb databases?
# For default that's set to 'yes' as it's almost always a win.
# If you want to save some CPU in the saving child set it to 'no' but
# the dataset will likely be bigger if you have compressible values or keys.
rdbcompression yes

# Since version 5 of RDB a CRC64 checksum is placed at the end of the file.
# This makes the format more resistant to corruption but there is a performance
# hit to pay (around 10%) when saving and loading RDB files, so you can disable it
# for maximum performances.
#
# RDB files created with checksum disabled have a checksum of zero that will
# tell the loading code to skip the check.
rdbchecksum yes

# The filename where to dump the DB
dbfilename dump.rdb

# The working directory.
#
# The DB will be written inside this directory, with the filename specified
# above using the 'dbfilename' configuration directive.
#
# The Append Only File will also be created inside this directory.
#
# Note that you must specify a directory here, not a file name.
dir ./
# ①、save：这里是用来配置触发 Redis的持久化条件，也就是什么时候将内存中的数据保存到硬盘。默认如下配置：
#    save 900 1：表示900 秒内如果至少有 1 个 key 的值变化，则保存
#    save 300 10：表示300 秒内如果至少有 10 个 key 的值变化，则保存
#    save 60 10000：表示60 秒内如果至少有 10000 个 key 的值变化，则保存
#　　 当然如果你只是用Redis的缓存功能，不需要持久化，那么你可以注释掉所有的 save 行来停用保存功能。可以直接一个空字符串来实现停用：save ""
# ②、stop-writes-on-bgsave-error ：默认值为yes。当启用了RDB且最后一次后台保存数据失败，Redis是否停止接收数据。这会让用户意识到数据没有正确持久化到磁盘上，否则没有人会注意到灾难（disaster）发生了。如果Redis重启了，那么又可以重新开始接收数据了
# ③、rdbcompression ；默认值是yes。对于存储到磁盘中的快照，可以设置是否进行压缩存储。如果是的话，redis会采用LZF算法进行压缩。如果你不想消耗CPU来进行压缩的话，可以设置为关闭此功能，但是存储在磁盘上的快照会比较大。
# ④、rdbchecksum ：默认值是yes。在存储快照后，我们还可以让redis使用CRC64算法来进行数据校验，但是这样做会增加大约10%的性能消耗，如果希望获取到最大的性能提升，可以关闭此功能。
# ⑤、dbfilename ：设置快照的文件名，默认是 dump.rdb
# ⑥、dir：设置快照文件的存放路径，这个配置项一定是个目录，而不能是文件名。使用上面的 dbfilename 作为保存的文件名。

################################# REPLICATION #################################

# Master-Slave replication. Use slaveof to make a Redis instance a copy of
# another Redis server. A few things to understand ASAP about Redis replication.
#
# 1) Redis replication is asynchronous, but you can configure a master to
#    stop accepting writes if it appears to be not connected with at least
#    a given number of slaves.
# 2) Redis slaves are able to perform a partial resynchronization with the
#    master if the replication link is lost for a relatively small amount of
#    time. You may want to configure the replication backlog size (see the next
#    sections of this file) with a sensible value depending on your needs.
# 3) Replication is automatic and does not need user intervention. After a
#    network partition slaves automatically try to reconnect to masters
#    and resynchronize with them.
#
# slaveof <masterip> <masterport>

# If the master is password protected (using the "requirepass" configuration
# directive below) it is possible to tell the slave to authenticate before
# starting the replication synchronization process, otherwise the master will
# refuse the slave request.
#
# masterauth <master-password>

# When a slave loses its connection with the master, or when the replication
# is still in progress, the slave can act in two different ways:
#
# 1) if slave-serve-stale-data is set to 'yes' (the default) the slave will
#    still reply to client requests, possibly with out of date data, or the
#    data set may just be empty if this is the first synchronization.
#
# 2) if slave-serve-stale-data is set to 'no' the slave will reply with
#    an error "SYNC with master in progress" to all the kind of commands
#    but to INFO and SLAVEOF.
#
slave-serve-stale-data yes

# You can configure a slave instance to accept writes or not. Writing against
# a slave instance may be useful to store some ephemeral data (because data
# written on a slave will be easily deleted after resync with the master) but
# may also cause problems if clients are writing to it because of a
# misconfiguration.
#
# Since Redis 2.6 by default slaves are read-only.
#
# Note: read only slaves are not designed to be exposed to untrusted clients
# on the internet. It's just a protection layer against misuse of the instance.
# Still a read only slave exports by default all the administrative commands
# such as CONFIG, DEBUG, and so forth. To a limited extent you can improve
# security of read only slaves using 'rename-command' to shadow all the
# administrative / dangerous commands.
slave-read-only yes

# Replication SYNC strategy: disk or socket.
#
# -------------------------------------------------------
# WARNING: DISKLESS REPLICATION IS EXPERIMENTAL CURRENTLY
# -------------------------------------------------------
#
# New slaves and reconnecting slaves that are not able to continue the replication
# process just receiving differences, need to do what is called a "full
# synchronization". An RDB file is transmitted from the master to the slaves.
# The transmission can happen in two different ways:
#
# 1) Disk-backed: The Redis master creates a new process that writes the RDB
#                 file on disk. Later the file is transferred by the parent
#                 process to the slaves incrementally.
# 2) Diskless: The Redis master creates a new process that directly writes the
#              RDB file to slave sockets, without touching the disk at all.
#
# With disk-backed replication, while the RDB file is generated, more slaves
# can be queued and served with the RDB file as soon as the current child producing
# the RDB file finishes its work. With diskless replication instead once
# the transfer starts, new slaves arriving will be queued and a new transfer
# will start when the current one terminates.
#
# When diskless replication is used, the master waits a configurable amount of
# time (in seconds) before starting the transfer in the hope that multiple slaves
# will arrive and the transfer can be parallelized.
#
# With slow disks and fast (large bandwidth) networks, diskless replication
# works better.
repl-diskless-sync no

# When diskless replication is enabled, it is possible to configure the delay
# the server waits in order to spawn the child that transfers the RDB via socket
# to the slaves.
#
# This is important since once the transfer starts, it is not possible to serve
# new slaves arriving, that will be queued for the next RDB transfer, so the server
# waits a delay in order to let more slaves arrive.
#
# The delay is specified in seconds, and by default is 5 seconds. To disable
# it entirely just set it to 0 seconds and the transfer will start ASAP.
repl-diskless-sync-delay 5

# Slaves send PINGs to server in a predefined interval. It's possible to change
# this interval with the repl_ping_slave_period option. The default value is 10
# seconds.
#
# repl-ping-slave-period 10

# The following option sets the replication timeout for:
#
# 1) Bulk transfer I/O during SYNC, from the point of view of slave.
# 2) Master timeout from the point of view of slaves (data, pings).
# 3) Slave timeout from the point of view of masters (REPLCONF ACK pings).
#
# It is important to make sure that this value is greater than the value
# specified for repl-ping-slave-period otherwise a timeout will be detected
# every time there is low traffic between the master and the slave.
#
# repl-timeout 60

# Disable TCP_NODELAY on the slave socket after SYNC?
#
# If you select "yes" Redis will use a smaller number of TCP packets and
# less bandwidth to send data to slaves. But this can add a delay for
# the data to appear on the slave side, up to 40 milliseconds with
# Linux kernels using a default configuration.
#
# If you select "no" the delay for data to appear on the slave side will
# be reduced but more bandwidth will be used for replication.
#
# By default we optimize for low latency, but in very high traffic conditions
# or when the master and slaves are many hops away, turning this to "yes" may
# be a good idea.
repl-disable-tcp-nodelay no

# Set the replication backlog size. The backlog is a buffer that accumulates
# slave data when slaves are disconnected for some time, so that when a slave
# wants to reconnect again, often a full resync is not needed, but a partial
# resync is enough, just passing the portion of data the slave missed while
# disconnected.
#
# The bigger the replication backlog, the longer the time the slave can be
# disconnected and later be able to perform a partial resynchronization.
#
# The backlog is only allocated once there is at least a slave connected.
#
# repl-backlog-size 1mb

# After a master has no longer connected slaves for some time, the backlog
# will be freed. The following option configures the amount of seconds that
# need to elapse, starting from the time the last slave disconnected, for
# the backlog buffer to be freed.
#
# A value of 0 means to never release the backlog.
#
# repl-backlog-ttl 3600

# The slave priority is an integer number published by Redis in the INFO output.
# It is used by Redis Sentinel in order to select a slave to promote into a
# master if the master is no longer working correctly.
#
# A slave with a low priority number is considered better for promotion, so
# for instance if there are three slaves with priority 10, 100, 25 Sentinel will
# pick the one with priority 10, that is the lowest.
#
# However a special priority of 0 marks the slave as not able to perform the
# role of master, so a slave with priority of 0 will never be selected by
# Redis Sentinel for promotion.
#
# By default the priority is 100.
slave-priority 100

# It is possible for a master to stop accepting writes if there are less than
# N slaves connected, having a lag less or equal than M seconds.
#
# The N slaves need to be in "online" state.
#
# The lag in seconds, that must be <= the specified value, is calculated from
# the last ping received from the slave, that is usually sent every second.
#
# This option does not GUARANTEE that N replicas will accept the write, but
# will limit the window of exposure for lost writes in case not enough slaves
# are available, to the specified number of seconds.
#
# For example to require at least 3 slaves with a lag <= 10 seconds use:
#
# min-slaves-to-write 3
# min-slaves-max-lag 10
#
# Setting one or the other to 0 disables the feature.
#
# By default min-slaves-to-write is set to 0 (feature disabled) and
# min-slaves-max-lag is set to 10.
# ①、slave-serve-stale-data：默认值为yes。当一个 slave 与 master 失去联系，或者复制正在进行的时候，slave 可能会有两种表现：
#  　 1) 如果为 yes ，slave 仍然会应答客户端请求，但返回的数据可能是过时，或者数据可能是空的在第一次同步的时候 
#  　 2) 如果为 no ，在你执行除了 info he salveof 之外的其他命令时，slave 都将返回一个 "SYNC with master in progress" 的错误
# ②、slave-read-only：配置Redis的Slave实例是否接受写操作，即Slave是否为只读Redis。默认值为yes。
# ③、repl-diskless-sync：主从数据复制是否使用无硬盘复制功能。默认值为no。
# ④、repl-diskless-sync-delay：当启用无硬盘备份，服务器等待一段时间后才会通过套接字向从站传送RDB文件，这个等待时间是可配置的。  这一点很重要，因为一旦传送开始，就不可能再为一个新到达的从站服务。从站则要排队等待下一次RDB传送。因此服务器等待一段  时间以期更多的从站到达。延迟时间以秒为单位，默认为5秒。要关掉这一功能，只需将它设置为0秒，传送会立即启动。默认值为5。
# ⑤、repl-disable-tcp-nodelay：同步之后是否禁用从站上的TCP_NODELAY 如果你选择yes，redis会使用较少量的TCP包和带宽向从站发送数据。但这会导致在从站增加一点数据的延时。  Linux内核默认配置情况下最多40毫秒的延时。如果选择no，从站的数据延时不会那么多，但备份需要的带宽相对较多。默认情况下我们将潜在因素优化，但在高负载情况下或者在主从站都跳的情况下，把它切换为yes是个好主意。默认值为no。



################################## SECURITY ###################################

# Require clients to issue AUTH <PASSWORD> before processing any other
# commands.  This might be useful in environments in which you do not trust
# others with access to the host running redis-server.
#
# This should stay commented out for backward compatibility and because most
# people do not need auth (e.g. they run their own servers).
#
# Warning: since Redis is pretty fast an outside user can try up to
# 150k passwords per second against a good box. This means that you should
# use a very strong password otherwise it will be very easy to break.
#
# requirepass foobared

# Command renaming.
#
# It is possible to change the name of dangerous commands in a shared
# environment. For instance the CONFIG command may be renamed into something
# hard to guess so that it will still be available for internal-use tools
# but not available for general clients.
#
# Example:
#
# rename-command CONFIG b840fc02d524045429941cc15f59e41cb7be6c52
#
# It is also possible to completely kill a command by renaming it into
# an empty string:
#
# rename-command CONFIG ""
#
# Please note that changing the name of commands that are logged into the
# AOF file or transmitted to slaves may cause problems.
# ①、rename-command：命令重命名，对于一些危险命令例如：
#　　　　flushdb（清空数据库）
#　　　　flushall（清空所有记录）
#　　　　config（客户端连接后可配置服务器）
#　　　　keys（客户端连接后可查看所有存在的键）                   
#　　作为服务端redis-server，常常需要禁用以上命令来使得服务器更加安全，禁用的具体做法是是：
# rename-command FLUSHALL ""
# 也可以保留命令但是不能轻易使用，重命名这个命令即可：
# rename-command FLUSHALL abcdefg
#　　这样，重启服务器后则需要使用新命令来执行操作，否则服务器会报错unknown command。
# ②、requirepass:设置redis连接密码
#　　比如: requirepass 123  表示redis的连接密码为123.

################################### LIMITS ####################################

# Set the max number of connected clients at the same time. By default
# this limit is set to 10000 clients, however if the Redis server is not
# able to configure the process file limit to allow for the specified limit
# the max number of allowed clients is set to the current file limit
# minus 32 (as Redis reserves a few file descriptors for internal uses).
#
# Once the limit is reached Redis will close all the new connections sending
# an error 'max number of clients reached'.
#
# maxclients 10000
# ①、maxclients ：设置客户端最大并发连接数，默认无限制，Redis可以同时打开的客户端连接数为Redis进程可以打开的最大文件。  描述符数-32（redis server自身会使用一些），如果设置 maxclients为0 。表示不作限制。当客户端连接数到达限制时，Redis会关闭新的连接并向客户端返回max number of clients reached错误信息

# If Redis is to be used as an in-memory-only cache without any kind of
# persistence, then the fork() mechanism used by the background AOF/RDB
# persistence is unnecessary. As an optimization, all persistence can be
# turned off in the Windows version of Redis. This will redirect heap
# allocations to the system heap allocator, and disable commands that would
# otherwise cause fork() operations: BGSAVE and BGREWRITEAOF.
# This flag may not be combined with any of the other flags that configure
# AOF and RDB operations.
# persistence-available [(yes)|no]

# Don't use more memory than the specified amount of bytes.
# When the memory limit is reached Redis will try to remove keys
# according to the eviction policy selected (see maxmemory-policy).
#
# If Redis can't remove keys according to the policy, or if the policy is
# set to 'noeviction', Redis will start to reply with errors to commands
# that would use more memory, like SET, LPUSH, and so on, and will continue
# to reply to read-only commands like GET.
#
# This option is usually useful when using Redis as an LRU cache, or to set
# a hard memory limit for an instance (using the 'noeviction' policy).
#
# WARNING: If you have slaves attached to an instance with maxmemory on,
# the size of the output buffers needed to feed the slaves are subtracted
# from the used memory count, so that network problems / resyncs will
# not trigger a loop where keys are evicted, and in turn the output
# buffer of slaves is full with DELs of keys evicted triggering the deletion
# of more keys, and so forth until the database is completely emptied.
#
# In short... if you have slaves attached it is suggested that you set a lower
# limit for maxmemory so that there is some free RAM on the system for slave
# output buffers (but this is not needed if the policy is 'noeviction').
#
# WARNING: not setting maxmemory will cause Redis to terminate with an
# out-of-memory exception if the heap limit is reached.
#
# NOTE: since Redis uses the system paging file to allocate the heap memory,
# the Working Set memory usage showed by the Windows Task Manager or by other
# tools such as ProcessExplorer will not always be accurate. For example, right
# after a background save of the RDB or the AOF files, the working set value
# may drop significantly. In order to check the correct amount of memory used
# by the redis-server to store the data, use the INFO client command. The INFO
# command shows only the memory used to store the redis data, not the extra
# memory used by the Windows process for its own requirements. Th3 extra amount
# of memory not reported by the INFO command can be calculated subtracting the
# Peak Working Set reported by the Windows Task Manager and the used_memory_peak
# reported by the INFO command.
#
# maxmemory <bytes>

# MAXMEMORY POLICY: how Redis will select what to remove when maxmemory
# is reached. You can select among five behaviors:
#
# volatile-lru -> remove the key with an expire set using an LRU algorithm
# allkeys-lru -> remove any key according to the LRU algorithm
# volatile-random -> remove a random key with an expire set
# allkeys-random -> remove a random key, any key
# volatile-ttl -> remove the key with the nearest expire time (minor TTL)
# noeviction -> don't expire at all, just return an error on write operations
#
# Note: with any of the above policies, Redis will return an error on write
#       operations, when there are no suitable keys for eviction.
#
#       At the date of writing these commands are: set setnx setex append
#       incr decr rpush lpush rpushx lpushx linsert lset rpoplpush sadd
#       sinter sinterstore sunion sunionstore sdiff sdiffstore zadd zincrby
#       zunionstore zinterstore hset hsetnx hmset hincrby incrby decrby
#       getset mset msetnx exec sort
#
# The default is:
#
# maxmemory-policy noeviction

# LRU and minimal TTL algorithms are not precise algorithms but approximated
# algorithms (in order to save memory), so you can tune it for speed or
# accuracy. For default Redis will check five keys and pick the one that was
# used less recently, you can change the sample size using the following
# configuration directive.
#
# The default of 5 produces good enough results. 10 Approximates very closely
# true LRU but costs a bit more CPU. 3 is very fast but not very accurate.
#
# maxmemory-samples 5

# ①、maxmemory：设置Redis的最大内存，如果设置为0 。表示不作限制。通常是配合下面介绍的maxmemory-policy参数一起使用。
# ②、maxmemory-policy ：当内存使用达到maxmemory设置的最大值时，redis使用的内存清除策略。有以下几种可以选择：
#　　　　1）volatile-lru   利用LRU算法移除设置过过期时间的key (LRU:最近使用 Least Recently Used ) 
#　　　　2）allkeys-lru   利用LRU算法移除任何key 
#　　　　3）volatile-random 移除设置过过期时间的随机key 
#　　　　4）allkeys-random  移除随机ke
#　　　　5）volatile-ttl   移除即将过期的key(minor TTL) 
#　　　　6）noeviction  noeviction   不移除任何key，只是返回一个写错误 ，默认选项
# ③、maxmemory-samples ：LRU 和 minimal TTL 算法都不是精准的算法，但是相对精确的算法(为了节省内存)。随意你可以选择样本大小进行检，redis默认选择3个样本进行检测，你可以通过maxmemory-samples进行设置样本数。


############################## APPEND ONLY MODE ###############################

# By default Redis asynchronously dumps the dataset on disk. This mode is
# good enough in many applications, but an issue with the Redis process or
# a power outage may result into a few minutes of writes lost (depending on
# the configured save points).
#
# The Append Only File is an alternative persistence mode that provides
# much better durability. For instance using the default data fsync policy
# (see later in the config file) Redis can lose just one second of writes in a
# dramatic event like a server power outage, or a single write if something
# wrong with the Redis process itself happens, but the operating system is
# still running correctly.
#
# AOF and RDB persistence can be enabled at the same time without problems.
# If the AOF is enabled on startup Redis will load the AOF, that is the file
# with the better durability guarantees.
#
# Please check http://redis.io/topics/persistence for more information.

appendonly no

# The name of the append only file (default: "appendonly.aof")
appendfilename "appendonly.aof"

# The fsync() call tells the Operating System to actually write data on disk
# instead of waiting for more data in the output buffer. Some OS will really flush
# data on disk, some other OS will just try to do it ASAP.
#
# Redis supports three different modes:
#
# no: don't fsync, just let the OS flush the data when it wants. Faster.
# always: fsync after every write to the append only log. Slow, Safest.
# everysec: fsync only one time every second. Compromise.
#
# The default is "everysec", as that's usually the right compromise between
# speed and data safety. It's up to you to understand if you can relax this to
# "no" that will let the operating system flush the output buffer when
# it wants, for better performances (but if you can live with the idea of
# some data loss consider the default persistence mode that's snapshotting),
# or on the contrary, use "always" that's very slow but a bit safer than
# everysec.
#
# More details please check the following article:
# http://antirez.com/post/redis-persistence-demystified.html
#
# If unsure, use "everysec".

# appendfsync always
appendfsync everysec
# appendfsync no

# When the AOF fsync policy is set to always or everysec, and a background
# saving process (a background save or AOF log background rewriting) is
# performing a lot of I/O against the disk, in some Linux configurations
# Redis may block too long on the fsync() call. Note that there is no fix for
# this currently, as even performing fsync in a different thread will block
# our synchronous write(2) call.
#
# In order to mitigate this problem it's possible to use the following option
# that will prevent fsync() from being called in the main process while a
# BGSAVE or BGREWRITEAOF is in progress.
#
# This means that while another child is saving, the durability of Redis is
# the same as "appendfsync none". In practical terms, this means that it is
# possible to lose up to 30 seconds of log in the worst scenario (with the
# default Linux settings).
#
# If you have latency problems turn this to "yes". Otherwise leave it as
# "no" that is the safest pick from the point of view of durability.
no-appendfsync-on-rewrite no

# Automatic rewrite of the append only file.
# Redis is able to automatically rewrite the log file implicitly calling
# BGREWRITEAOF when the AOF log size grows by the specified percentage.
#
# This is how it works: Redis remembers the size of the AOF file after the
# latest rewrite (if no rewrite has happened since the restart, the size of
# the AOF at startup is used).
#
# This base size is compared to the current size. If the current size is
# bigger than the specified percentage, the rewrite is triggered. Also
# you need to specify a minimal size for the AOF file to be rewritten, this
# is useful to avoid rewriting the AOF file even if the percentage increase
# is reached but it is still pretty small.
#
# Specify a percentage of zero in order to disable the automatic AOF
# rewrite feature.

auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb

# An AOF file may be found to be truncated at the end during the Redis
# startup process, when the AOF data gets loaded back into memory.
# This may happen when the system where Redis is running
# crashes, especially when an ext4 filesystem is mounted without the
# data=ordered option (however this can't happen when Redis itself
# crashes or aborts but the operating system still works correctly).
#
# Redis can either exit with an error when this happens, or load as much
# data as possible (the default now) and start if the AOF file is found
# to be truncated at the end. The following option controls this behavior.
#
# If aof-load-truncated is set to yes, a truncated AOF file is loaded and
# the Redis server starts emitting a log to inform the user of the event.
# Otherwise if the option is set to no, the server aborts with an error
# and refuses to start. When the option is set to no, the user requires
# to fix the AOF file using the "redis-check-aof" utility before to restart
# the server.
#
# Note that if the AOF file will be found to be corrupted in the middle
# the server will still exit with an error. This option only applies when
# Redis will try to read more data from the AOF file but not enough bytes
# will be found.
aof-load-truncated yes

# ①、appendonly：默认redis使用的是rdb方式持久化，这种方式在许多应用中已经足够用了。但是redis如果中途宕机，会导致可能有几分钟的数据丢失，根据save来策略进行持久化，Append Only File是另一种持久化方式，  可以提供更好的持久化特性。Redis会把每次写入的数据在接收后都写入appendonly.aof文件，每次启动时Redis都会先把这个文件的数据读入内存里，先忽略RDB文件。默认值为no。
#　　②、appendfilename ：aof文件名，默认是"appendonly.aof"
#　　③、appendfsync：aof持久化策略的配置；no表示不执行fsync，由操作系统保证数据同步到磁盘，速度最快；always表示每次写入都执行fsync，以保证数据同步到磁盘；everysec表示每秒执行一次fsync，可能会导致丢失这1s数据
#　　④、no-appendfsync-on-rewrite：在aof重写或者写入rdb文件的时候，会执行大量IO，此时对于everysec和always的aof模式来说，执行fsync会造成阻塞过长时间，no-appendfsync-on-rewrite字段设置为默认设置为no。如果对延迟要求很高的应用，这个字段可以设置为yes，否则还是设置为no，这样对持久化特性来说这是更安全的选择。   设置为yes表示rewrite期间对新写操作不fsync,暂时存在内存中,等rewrite完成后再写入，默认为no，建议yes。Linux的默认fsync策略是30秒。可能丢失30秒数据。默认值为no。
#　　⑤、auto-aof-rewrite-percentage：默认值为100。aof自动重写配置，当目前aof文件大小超过上一次重写的aof文件大小的百分之多少进行重写，即当aof文件增长到一定大小的时候，Redis能够调用bgrewriteaof对日志文件进行重写。当前AOF文件大小是上次日志重写得到AOF文件大小的二倍（设置为100）时，自动启动新的日志重写过程。
#　　⑥、auto-aof-rewrite-min-size：64mb。设置允许重写的最小aof文件大小，避免了达到约定百分比但尺寸仍然很小的情况还要重写。
#　　⑦、aof-load-truncated：aof文件可能在尾部是不完整的，当redis启动的时候，aof文件的数据被载入内存。重启可能发生在redis所在的主机操作系统宕机后，尤其在ext4文件系统没有加上data=ordered选项，出现这种现象  redis宕机或者异常终止不会造成尾部不完整现象，可以选择让redis退出，或者导入尽可能多的数据。如果选择的是yes，当截断的aof文件被导入的时候，会自动发布一个log给客户端然后load。如果是no，用户必须手动redis-check-aof修复AOF文件才可以。默认值为 yes。

################################ LUA SCRIPTING  ###############################

# Max execution time of a Lua script in milliseconds.
#
# If the maximum execution time is reached Redis will log that a script is
# still in execution after the maximum allowed time and will start to
# reply to queries with an error.
#
# When a long running script exceeds the maximum execution time only the
# SCRIPT KILL and SHUTDOWN NOSAVE commands are available. The first can be
# used to stop a script that did not yet called write commands. The second
# is the only way to shut down the server in the case a write command was
# already issued by the script but the user doesn't want to wait for the natural
# termination of the script.
#
# Set it to 0 or a negative value for unlimited execution without warnings.
lua-time-limit 5000

# ①、lua-time-limit：一个lua脚本执行的最大时间，单位为ms。默认值为5000.


################################ REDIS CLUSTER  ###############################
#
# ++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
# WARNING EXPERIMENTAL: Redis Cluster is considered to be stable code, however
# in order to mark it as "mature" we need to wait for a non trivial percentage
# of users to deploy it in production.
# ++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
#
# Normal Redis instances can't be part of a Redis Cluster; only nodes that are
# started as cluster nodes can. In order to start a Redis instance as a
# cluster node enable the cluster support uncommenting the following:
#
# cluster-enabled yes

# Every cluster node has a cluster configuration file. This file is not
# intended to be edited by hand. It is created and updated by Redis nodes.
# Every Redis Cluster node requires a different cluster configuration file.
# Make sure that instances running in the same system do not have
# overlapping cluster configuration file names.
#
# cluster-config-file nodes-6379.conf

# Cluster node timeout is the amount of milliseconds a node must be unreachable
# for it to be considered in failure state.
# Most other internal time limits are multiple of the node timeout.
#
# cluster-node-timeout 15000

# A slave of a failing master will avoid to start a failover if its data
# looks too old.
#
# There is no simple way for a slave to actually have a exact measure of
# its "data age", so the following two checks are performed:
#
# 1) If there are multiple slaves able to failover, they exchange messages
#    in order to try to give an advantage to the slave with the best
#    replication offset (more data from the master processed).
#    Slaves will try to get their rank by offset, and apply to the start
#    of the failover a delay proportional to their rank.
#
# 2) Every single slave computes the time of the last interaction with
#    its master. This can be the last ping or command received (if the master
#    is still in the "connected" state), or the time that elapsed since the
#    disconnection with the master (if the replication link is currently down).
#    If the last interaction is too old, the slave will not try to failover
#    at all.
#
# The point "2" can be tuned by user. Specifically a slave will not perform
# the failover if, since the last interaction with the master, the time
# elapsed is greater than:
#
#   (node-timeout * slave-validity-factor) + repl-ping-slave-period
#
# So for example if node-timeout is 30 seconds, and the slave-validity-factor
# is 10, and assuming a default repl-ping-slave-period of 10 seconds, the
# slave will not try to failover if it was not able to talk with the master
# for longer than 310 seconds.
#
# A large slave-validity-factor may allow slaves with too old data to failover
# a master, while a too small value may prevent the cluster from being able to
# elect a slave at all.
#
# For maximum availability, it is possible to set the slave-validity-factor
# to a value of 0, which means, that slaves will always try to failover the
# master regardless of the last time they interacted with the master.
# (However they'll always try to apply a delay proportional to their
# offset rank).
#
# Zero is the only value able to guarantee that when all the partitions heal
# the cluster will always be able to continue.
#
# cluster-slave-validity-factor 10

# Cluster slaves are able to migrate to orphaned masters, that are masters
# that are left without working slaves. This improves the cluster ability
# to resist to failures as otherwise an orphaned master can't be failed over
# in case of failure if it has no working slaves.
#
# Slaves migrate to orphaned masters only if there are still at least a
# given number of other working slaves for their old master. This number
# is the "migration barrier". A migration barrier of 1 means that a slave
# will migrate only if there is at least 1 other working slave for its master
# and so forth. It usually reflects the number of slaves you want for every
# master in your cluster.
#
# Default is 1 (slaves migrate only if their masters remain with at least
# one slave). To disable migration just set it to a very large value.
# A value of 0 can be set but is useful only for debugging and dangerous
# in production.
#
# cluster-migration-barrier 1

# By default Redis Cluster nodes stop accepting queries if they detect there
# is at least an hash slot uncovered (no available node is serving it).
# This way if the cluster is partially down (for example a range of hash slots
# are no longer covered) all the cluster becomes, eventually, unavailable.
# It automatically returns available as soon as all the slots are covered again.
#
# However sometimes you want the subset of the cluster which is working,
# to continue to accept queries for the part of the key space that is still
# covered. In order to do so, just set the cluster-require-full-coverage
# option to no.
#
# cluster-require-full-coverage yes

# In order to setup your cluster make sure to read the documentation
# available at http://redis.io web site.

################################## SLOW LOG ###################################

# The Redis Slow Log is a system to log queries that exceeded a specified
# execution time. The execution time does not include the I/O operations
# like talking with the client, sending the reply and so forth,
# but just the time needed to actually execute the command (this is the only
# stage of command execution where the thread is blocked and can not serve
# other requests in the meantime).
#
# You can configure the slow log with two parameters: one tells Redis
# what is the execution time, in microseconds, to exceed in order for the
# command to get logged, and the other parameter is the length of the
# slow log. When a new command is logged the oldest one is removed from the
# queue of logged commands.

# The following time is expressed in microseconds, so 1000000 is equivalent
# to one second. Note that a negative number disables the slow log, while
# a value of zero forces the logging of every command.
slowlog-log-slower-than 10000

# There is no limit to this length. Just be aware that it will consume memory.
# You can reclaim memory used by the slow log with SLOWLOG RESET.
slowlog-max-len 128

################################ LATENCY MONITOR ##############################

# The Redis latency monitoring subsystem samples different operations
# at runtime in order to collect data related to possible sources of
# latency of a Redis instance.
#
# Via the LATENCY command this information is available to the user that can
# print graphs and obtain reports.
#
# The system only logs operations that were performed in a time equal or
# greater than the amount of milliseconds specified via the
# latency-monitor-threshold configuration directive. When its value is set
# to zero, the latency monitor is turned off.
#
# By default latency monitoring is disabled since it is mostly not needed
# if you don't have latency issues, and collecting data has a performance
# impact, that while very small, can be measured under big load. Latency
# monitoring can easily be enabled at runtime using the command
# "CONFIG SET latency-monitor-threshold <milliseconds>" if needed.
latency-monitor-threshold 0

############################# EVENT NOTIFICATION ##############################

# Redis can notify Pub/Sub clients about events happening in the key space.
# This feature is documented at http://redis.io/topics/notifications
#
# For instance if keyspace events notification is enabled, and a client
# performs a DEL operation on key "foo" stored in the Database 0, two
# messages will be published via Pub/Sub:
#
# PUBLISH __keyspace@0__:foo del
# PUBLISH __keyevent@0__:del foo
#
# It is possible to select the events that Redis will notify among a set
# of classes. Every class is identified by a single character:
#
#  K     Keyspace events, published with __keyspace@<db>__ prefix.
#  E     Keyevent events, published with __keyevent@<db>__ prefix.
#  g     Generic commands (non-type specific) like DEL, EXPIRE, RENAME, ...
#  $     String commands
#  l     List commands
#  s     Set commands
#  h     Hash commands
#  z     Sorted set commands
#  x     Expired events (events generated every time a key expires)
#  e     Evicted events (events generated when a key is evicted for maxmemory)
#  A     Alias for g$lshzxe, so that the "AKE" string means all the events.
#
#  The "notify-keyspace-events" takes as argument a string that is composed
#  of zero or multiple characters. The empty string means that notifications
#  are disabled.
#
#  Example: to enable list and generic events, from the point of view of the
#           event name, use:
#
#  notify-keyspace-events Elg
#
#  Example 2: to get the stream of the expired keys subscribing to channel
#             name __keyevent@0__:expired use:
#
#  notify-keyspace-events Ex
#
#  By default all notifications are disabled because most users don't need
#  this feature and the feature has some overhead. Note that if you don't
#  specify at least one of K or E, no events will be delivered.
notify-keyspace-events ""

############################### ADVANCED CONFIG ###############################

# Hashes are encoded using a memory efficient data structure when they have a
# small number of entries, and the biggest entry does not exceed a given
# threshold. These thresholds can be configured using the following directives.
hash-max-ziplist-entries 512
hash-max-ziplist-value 64

# Lists are also encoded in a special way to save a lot of space.
# The number of entries allowed per internal list node can be specified
# as a fixed maximum size or a maximum number of elements.
# For a fixed maximum size, use -5 through -1, meaning:
# -5: max size: 64 Kb  <-- not recommended for normal workloads
# -4: max size: 32 Kb  <-- not recommended
# -3: max size: 16 Kb  <-- probably not recommended
# -2: max size: 8 Kb   <-- good
# -1: max size: 4 Kb   <-- good
# Positive numbers mean store up to _exactly_ that number of elements
# per list node.
# The highest performing option is usually -2 (8 Kb size) or -1 (4 Kb size),
# but if your use case is unique, adjust the settings as necessary.
list-max-ziplist-size -2

# Lists may also be compressed.
# Compress depth is the number of quicklist ziplist nodes from *each* side of
# the list to *exclude* from compression.  The head and tail of the list
# are always uncompressed for fast push/pop operations.  Settings are:
# 0: disable all list compression
# 1: depth 1 means "don't start compressing until after 1 node into the list,
#    going from either the head or tail"
#    So: [head]->node->node->...->node->[tail]
#    [head], [tail] will always be uncompressed; inner nodes will compress.
# 2: [head]->[next]->node->node->...->node->[prev]->[tail]
#    2 here means: don't compress head or head->next or tail->prev or tail,
#    but compress all nodes between them.
# 3: [head]->[next]->[next]->node->node->...->node->[prev]->[prev]->[tail]
# etc.
list-compress-depth 0

# Sets have a special encoding in just one case: when a set is composed
# of just strings that happen to be integers in radix 10 in the range
# of 64 bit signed integers.
# The following configuration setting sets the limit in the size of the
# set in order to use this special memory saving encoding.
set-max-intset-entries 512

# Similarly to hashes and lists, sorted sets are also specially encoded in
# order to save a lot of space. This encoding is only used when the length and
# elements of a sorted set are below the following limits:
zset-max-ziplist-entries 128
zset-max-ziplist-value 64

# HyperLogLog sparse representation bytes limit. The limit includes the
# 16 bytes header. When an HyperLogLog using the sparse representation crosses
# this limit, it is converted into the dense representation.
#
# A value greater than 16000 is totally useless, since at that point the
# dense representation is more memory efficient.
#
# The suggested value is ~ 3000 in order to have the benefits of
# the space efficient encoding without slowing down too much PFADD,
# which is O(N) with the sparse encoding. The value can be raised to
# ~ 10000 when CPU is not a concern, but space is, and the data set is
# composed of many HyperLogLogs with cardinality in the 0 - 15000 range.
hll-sparse-max-bytes 3000

# Active rehashing uses 1 millisecond every 100 milliseconds of CPU time in
# order to help rehashing the main Redis hash table (the one mapping top-level
# keys to values). The hash table implementation Redis uses (see dict.c)
# performs a lazy rehashing: the more operation you run into a hash table
# that is rehashing, the more rehashing "steps" are performed, so if the
# server is idle the rehashing is never complete and some more memory is used
# by the hash table.
#
# The default is to use this millisecond 10 times every second in order to
# actively rehash the main dictionaries, freeing memory when possible.
#
# If unsure:
# use "activerehashing no" if you have hard latency requirements and it is
# not a good thing in your environment that Redis can reply from time to time
# to queries with 2 milliseconds delay.
#
# use "activerehashing yes" if you don't have such hard requirements but
# want to free memory asap when possible.
activerehashing yes

# The client output buffer limits can be used to force disconnection of clients
# that are not reading data from the server fast enough for some reason (a
# common reason is that a Pub/Sub client can't consume messages as fast as the
# publisher can produce them).
#
# The limit can be set differently for the three different classes of clients:
#
# normal -> normal clients including MONITOR clients
# slave  -> slave clients
# pubsub -> clients subscribed to at least one pubsub channel or pattern
#
# The syntax of every client-output-buffer-limit directive is the following:
#
# client-output-buffer-limit <class> <hard limit> <soft limit> <soft seconds>
#
# A client is immediately disconnected once the hard limit is reached, or if
# the soft limit is reached and remains reached for the specified number of
# seconds (continuously).
# So for instance if the hard limit is 32 megabytes and the soft limit is
# 16 megabytes / 10 seconds, the client will get disconnected immediately
# if the size of the output buffers reach 32 megabytes, but will also get
# disconnected if the client reaches 16 megabytes and continuously overcomes
# the limit for 10 seconds.
#
# By default normal clients are not limited because they don't receive data
# without asking (in a push way), but just after a request, so only
# asynchronous clients may create a scenario where data is requested faster
# than it can read.
#
# Instead there is a default limit for pubsub and slave clients, since
# subscribers and slaves receive data in a push fashion.
#
# Both the hard or the soft limit can be disabled by setting them to zero.
client-output-buffer-limit normal 0 0 0
client-output-buffer-limit slave 256mb 64mb 60
client-output-buffer-limit pubsub 32mb 8mb 60

# Redis calls an internal function to perform many background tasks, like
# closing connections of clients in timeot, purging expired keys that are
# never requested, and so forth.
#
# Not all tasks are perforemd with the same frequency, but Redis checks for
# tasks to perform according to the specified "hz" value.
#
# By default "hz" is set to 10. Raising the value will use more CPU when
# Redis is idle, but at the same time will make Redis more responsive when
# there are many keys expiring at the same time, and timeouts may be
# handled with more precision.
#
# The range is between 1 and 500, however a value over 100 is usually not
# a good idea. Most users should use the default of 10 and raise this up to
# 100 only in environments where very low latency is required.
hz 10

# When a child rewrites the AOF file, if the following option is enabled
# the file will be fsync-ed every 32 MB of data generated. This is useful
# in order to commit the file to the disk more incrementally and avoid
# big latency spikes.
aof-rewrite-incremental-fsync yes

################################## INCLUDES ###################################

# Include one or more other config files here.  This is useful if you
# have a standard template that goes to all Redis server but also need
# to customize a few per-server settings.  Include files can include
# other files, so use this wisely.
#
# include /path/to/local.conf
# include /path/to/other.conf


# ①、cluster-enabled：集群开关，默认是不开启集群模式。
# ②、cluster-config-file：集群配置文件的名称，每个节点都有一个集群相关的配置文件，持久化保存集群的信息。 这个文件并不需要手动配置，这个配置文件有Redis生成并更新，每个Redis集群节点需要一个单独的配置文件。请确保与实例运行的系统中配置文件名称不冲突。默认配置为nodes-6379.conf。
# ③、cluster-node-timeout ：可以配置值为15000。节点互连超时的阀值，集群节点超时毫秒数
# ④、cluster-slave-validity-factor ：可以配置值为10。在进行故障转移的时候，全部slave都会请求申请为master，但是有些slave可能与master断开连接一段时间了，  导致数据过于陈旧，这样的slave不应该被提升为master。该参数就是用来判断slave节点与master断线的时间是否过长。判断方法是：比较slave断开连接的时间和(node-timeout * slave-validity-factor) + repl-ping-slave-period     如果节点超时时间为三十秒, 并且slave-validity-factor为10,假设默认的repl-ping-slave-period是10秒，即如果超过310秒slave将不会尝试进行故障转移
# ⑤、cluster-migration-barrier ：可以配置值为1。master的slave数量大于该值，slave才能迁移到其他孤立master上，如这个参数若被设为2，那么只有当一个主节点拥有2 个可工作的从节点时，它的一个从节点会尝试迁移。
# ⑥、cluster-require-full-coverage：默认情况下，集群全部的slot有节点负责，集群状态才为ok，才能提供服务。  设置为no，可以在slot没有全部分配的时候提供服务。不建议打开该配置，这样会造成分区的时候，小分区的master一直在接受写请求，而造成很长时间数据不一致。
```

# Redis应用

## 1. Redis基础类型

### 1.1 Redis 的五种基本数据类型

![](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1Pmpz5ftMEn1xpdGVqjkEhEQD5fY3qBGEfZ4xNsqAuBOqFQe3rI8g97aDib733SDlCFLtxHZn1gkM4iczw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1#id=ZBaDx&originHeight=816&originWidth=908&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

#### String（字符串）

- 简介:String是Redis最基础的数据结构类型，它是二进制安全的，可以存储图片或者序列化的对象，值最大存储为512M
- 简单使用举例: `set key value`、`get key`等
- 应用场景：共享session、分布式锁，计数器、限流。
- 内部编码有3种，`int（8字节长整型）/embstr（小于等于39字节字符串）/raw（大于39个字节字符串）C语言的字符串是`char[]`实现的，而Redis使用**SDS（simple dynamic string）** 封装，sds源码如下：

```c
struct sdshdr{
  unsigned int len; // 标记buf的长度
  unsigned int free; //标记buf中未使用的元素个数
  char buf[]; // 存放元素的坑
}
```

SDS 结构图如下：![](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1Pmpz5ftMEn1xpdGVqjkEhEQD5cJg67LmHKrS1brhxJr6c2UjpgyWHIQWwKPhQNEqmtev8TYsjp3fibpg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1#id=AyuZ8&originHeight=403&originWidth=1080&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

Redis为什么选择**SDS**结构，而C语言原生的`char[]`不香吗？

```
举例其中一点，SDS中，O(1)时间复杂度，就可以获取字符串长度；而C 字符串，需要遍历整个字符串，时间复杂度为O(n)
```

- `
| **命令** | **描述** | **用法** |
| --- | --- | --- |
| SET | （1）将字符串值Value关联到Key（2）Key已关联则覆盖，无视类型（3）原本Key带有生存时间TTL，那么TTL被清除 | SET key value [EX seconds] [PX milliseconds] [NX&#124;XX] |
| GET | （1）返回key关联的字符串值（2）Key不存在返回nil（3）Key存储的不是字符串，返回错误，因为GET只用于处理字符串 | GET key |
| MSET | （1）同时设置一个或多个Key-Value键值对（2）某个给定Key已经存在，那么MSET新值会覆盖旧值（3）如果上面的覆盖不是希望的，那么使用MSETNX命令，所有Key都不存在才会进行覆盖（4）**MSET是一个原子性操作**，所有Key都会在同一时间被设置，不会存在有些更新有些没更新的情况 | MSET key value [key value ...] |
| MGET | （1）返回一个或多个给定Key对应的Value（2）某个Key不存在那么这个Key返回nil | MGET key [key ...] |
| SETEX | （1）将Value关联到Key（2）设置Key生存时间为seconds，单位为秒（3）如果Key对应的Value已经存在，则覆盖旧值（4）SET也可以设置失效时间，但是不同在于SETNX是一个原子操作，即关联值与设置生存时间同一时间完成 | SETEX key seconds value |
| SETNX | （1）将Key的值设置为Value，当且仅当Key不存在（2）若给定的Key已经存在，SEXNX不做任何动作 | SETNX key value |


```
set name "zhangsan"  		# 设置name值为zhangsan
set name "zhanssan"  100    # 设置name值为zhansgan，过期时间100秒
get name   					# 获取name的值
ttl name   					# 返回key剩余过期时间，单位秒
```

下面介绍一种特殊的Key-Value操作即INCR/DECR，可以利用Redis自动帮助我们对一个Key对应的Value进行加减，用表格看一下相关命令：

| **命令** | **描述** | **用法** |
| --- | --- | --- |
| INCR | （1）Key中存储的数字值+1，返回增加之后的值（2）Key不存在，那么Key的值被初始化为0再执行INCR（3）如果值包含错误类型或者字符串不能被表示为数字，那么返回错误（4）值限制在64位有符号数字表示之内，即-9223372036854775808~9223372036854775807 | INCR key |
| DECR | （1）Key中存储的数字值-1（2）其余同INCR | DECR key |
| INCRBY | （1）将key所存储的值加上增量返回增加之后的值（2）其余同INCR | INCRBY key increment |
| DECRBY | （1）将key所存储的值减去减量decrement（2）其余同INCR | DECRBY key decrement |


```
set k1 1
get k1
incr k1
decr k1
```

#### Hash（哈希）

- 简介：在Redis中，哈希类型是指v（值）本身又是一个键值对（k-v）结构
- 简单使用举例：`hset key field value` 、`hget key field`
- 内部编码：`ziplist（压缩列表）` 、`hashtable（哈希表）`
- 应用场景：缓存用户信息等。
- **注意点**：如果开发使用hgetall，哈希元素比较多的话，可能导致Redis阻塞，可以使用hscan。而如果只是获取部分field，建议使用hmget。

字符串和哈希类型对比如下图：![](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1Pmpz5ftMEn1xpdGVqjkEhEQD5oYeCs8QVnRvDlibgT55ibz5PvnUjsx8IFPvTGzf3rcWicyaib7icLwMFLPw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1#id=FBgl5&originHeight=611&originWidth=1065&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

| **命令** | **描述** | **用法** |
| --- | --- | --- |
| HSET | （1）将哈希表Key中的域field的值设为value（2）key不存在，一个新的Hash表被创建（3）field已经存在，旧的值被覆盖 | HSET key field value |
| HGET | （1）返回哈希表key中给定域field的值 | HGET key field |
| HDEL | （1）删除哈希表key中的一个或多个指定域（2）不存在的域将被忽略 | HDEL key filed [field ...] |
| HEXISTS | （1）查看哈希表key中，给定域field是否存在，存在返回1，不存在返回0 | HEXISTS key field |
| HGETALL | （1）返回哈希表key中，所有的域和值 | HGETALL key |
| HINCRBY | （1）为哈希表key中的域field加上增量increment（2）其余同INCR命令 | HINCRYBY key filed increment |
| HKEYS | （1）返回哈希表key中的所有域 | HKEYS key |
| HLEN | （1）返回哈希表key中域的数量 | HLEN key |
| HMGET | （1）返回哈希表key中，一个或多个给定域的值（2）如果给定的域不存在于哈希表，那么返回一个nil值 | HMGET key field [field ...] |
| HMSET | （1）同时将多个field-value对设置到哈希表key中（2）会覆盖哈希表中已存在的域（3）key不存在，那么一个空哈希表会被创建并执行HMSET操作 | HMSET key field value [field value ...] |
| HVALS | （1）返回哈希表key中所有的域和值 | HVALS key |


```
hset class zhangsan zhangsaninfo
hget class zhangsan
hdel class zhangsan
hexists class zhangsan
kgetall class
```

#### List（列表）

- 简介：列表（list）类型是用来存储多个有序的字符串，一个列表最多可以存储2^32-1个元素。
- 简单实用举例：`lpush key value [value ...]` 、`lrange key start end`
- 内部编码：ziplist（压缩列表）、linkedlist（链表）
- 应用场景：消息队列，文章列表,

一图看懂list类型的插入与弹出：![](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1Pmpz5ftMEn1xpdGVqjkEhEQD5icXiaBLzah1ia95rHO9WZRpjqKxsXXLZ4HNRyial1P5fmByDRGz4QiaLDBA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1#id=twvoA&originHeight=403&originWidth=1080&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

list应用场景参考以下：

```
lpush+lpop=Stack（栈）
lpush+rpop=Queue（队列）
lpsh+ltrim=Capped Collection（有限集合）
lpush+brpop=Message Queue（消息队列）
```
| **命令** | **描述** | **用法** |
| --- | --- | --- |
| LPUSH | （1）将一个或多个值value插入到列表key的表头（2）如果有多个value值，那么各个value值按从左到右的顺序依次插入表头（3）key不存在，一个空列表会被创建并执行LPUSH操作（4）key存在但不是列表类型，返回错误 | LPUSH key value [value ...] |
| LPUSHX | （1）将值value插入到列表key的表头，当且晋档key存在且为一个列表（2）key不存在时，LPUSHX命令什么都不做 | LPUSHX key value |
| LPOP | （1）移除并返回列表key的头元素 | LPOP key |
| LRANGE | （1）返回列表key中指定区间内的元素，区间以偏移量start和stop指定（2）start和stop都以0位底（3）可使用负数下标，-1表示列表最后一个元素，-2表示列表倒数第二个元素，以此类推（4）start大于列表最大下标，返回空列表（5）stop大于列表最大下标，stop=列表最大下标 | LRANGE key start stop |
| LREM | （1）根据count的值，移除列表中与value相等的元素（2）count>0表示从头到尾搜索，移除与value相等的元素，数量为count（3）count<0表示从从尾到头搜索，移除与value相等的元素，数量为count（4）count=0表示移除表中所有与value相等的元素 | LREM key count value |
| LSET | （1）将列表key下标为index的元素值设为value（2）index参数超出范围，或对一个空列表进行LSET时，返回错误 | LSET key index value |
| LINDEX | （1）返回列表key中，下标为index的元素 | LINDEX key index |
| LINSERT | （1）将值value插入列表key中，位于pivot前面或者后面（2）pivot不存在于列表key时，不执行任何操作（3）key不存在，不执行任何操作 | LINSERT key BEFORE&#124;AFTER pivot value |
| LLEN | （1）返回列表key的长度（2）key不存在，返回0 | LLEN key |
| LTRIM | （1）对一个列表进行修剪，让列表只返回指定区间内的元素，不存在指定区间内的都将被移除 | LTRIM key start stop |
| RPOP | （1）移除并返回列表key的尾元素 | RPOP key |
| RPOPLPUSH | 在一个原子时间内，执行两个动作：（1）将列表source中最后一个元素弹出并返回给客户端（2）将source弹出的元素插入到列表desination，作为destination列表的头元素 | RPOPLPUSH source destination |
| RPUSH | （1）将一个或多个值value插入到列表key的表尾 | RPUSH key value [value ...] |
| RPUSHX | （1）将value插入到列表key的表尾，当且仅当key存在并且是一个列表（2）key不存在，RPUSHX什么都不做 | RPUSHX key value |


#### Set（集合）

![](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1Pmpz5ftMEn1xpdGVqjkEhEQD5wG2AZAoL8TSH7nYhh2tugLrFjMGLDOIk3icu6Wjt7hJ4bCPuZC5icGpA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1#id=Ercs4&originHeight=411&originWidth=1073&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

- 简介：集合（set）类型也是用来保存多个的字符串元素，但是不允许重复元素
- 简单使用举例：`sadd key element [element ...]`、`smembers key`
- 内部编码：`intset（整数集合）`、`hashtable（哈希表）`
- **注意点**：smembers和lrange、hgetall都属于比较重的命令，如果元素过多存在阻塞Redis的可能性，可以使用sscan来完成。
- 应用场景：用户标签,生成随机数抽奖、社交需求。
| **命令** | **描述** | **用法** |
| --- | --- | --- |
| SADD | （1）将一个或多个member元素加入到key中，已存在在集合的member将被忽略（2）假如key不存在，则只创建一个只包含member元素做成员的集合（3）当key不是集合类型时，将返回一个错误 | SADD key number [member ...] |
| SCARD | （1）返回key对应的集合中的元素数量 | SCARD key |
| SDIFF | （1）返回一个集合的全部成员，该集合是第一个Key对应的集合和后面key对应的集合的差集 | SDIFF key [key ...] |
| SDIFFSTORE | （1）和SDIFF类似，但结果保存到destination集合而不是简单返回结果集（2） destination如果已存在，则覆盖 | SDIFFSTORE destionation key [key ...] |
| SINTER | （1）返回一个集合的全部成员，该集合是所有给定集合的交集（2）不存在的key被视为空集 | SINTER key [key ...] |
| SINTERSTORE | （1）和SINTER类似，但结果保存早destination集合而不是简单返回结果集（2）如果destination已存在，则覆盖（3）destination可以是key本身 | SINTERSTORE destination key [key ...] |
| SISMEMBER | （1）判断member元素是否key的成员，0表示不是，1表示是 | SISMEMBER key member |
| SMEMBERS | （1）返回集合key中的所有成员（2）不存在的key被视为空集 | SMEMBERS key |
| SMOVE | （1）原子性地将member元素从source集合移动到destination集合（2）source集合中不包含member元素，SMOVE命令不执行任何操作，仅返回0（3）destination中已包含member元素，SMOVE命令只是简单做source集合的member元素移除 | SMOVE source desination member |
| SPOP | （1）移除并返回集合中的一个随机元素，如果count不指定那么随机返回一个随机元素（2）count为正数且小于集合元素数量，那么返回一个count个元素的数组且数组中的**元素各不相同**（3）count为正数且大于等于集合元素数量，那么返回整个集合（4）count为负数那么命令返回一个数组，数组中的**元素可能重复多次**，数量为count的绝对值 | SPOP key [count] |
| SRANDMEMBER | （1）如果count不指定，那么返回集合中的一个随机元素（2）count同上 | SRANDMEMBER key [count] |
| SREM | （1）移除集合key中的一个或多个member元素，不存在的member将被忽略 | SREM key member [member ...] |
| SUNION | （1）返回一个集合的全部成员，该集合是所有给定集合的并集（2）不存在的key被视为空集 | SUNION key [key ...] |
| SUNIONSTORE | （1）类似SUNION，但结果保存到destination集合而不是简单返回结果集（2）destination已存在，覆盖旧值（3）destination可以是key本身 | SUNION destination key [key ...] |


#### 有序集合（zset）

- 简介：已排序的字符串集合，同时元素不能重复
- 简单格式举例：`zadd key score member [score member ...]`，`zrank key member`
- 底层内部编码：`ziplist（压缩列表）`、`skiplist（跳跃表）`
- 应用场景：排行榜，社交需求（如用户点赞）。
| **命令** | **描述** | **用法** |
| --- | --- | --- |
| ZADD | （1）将一个或多个member元素及其score值加入有序集key中（2）如果member已经是有序集的成员，那么更新member对应的score并重新插入member保证member在正确的位置上（3）score可以是整数值或双精度浮点数 | ZADD key score member [[score member] [score member] ...] |
| ZCARD | （1）返回有序集key的元素个数 | ZCARD key |
| ZCOUNT | （1） 返回有序集key中，score值>=min且<=max的成员的数量 | ZCOUNT key min max |
| ZRANGE | （1）返回有序集key中指定区间内的成员，成员位置按score从小到大排序（2）具有相同score值的成员按字典序排列（3）需要成员按score从大到小排列，使用ZREVRANGE命令（4）下标参数start和stop都以0为底，也可以用负数，-1表示最后一个成员，-2表示倒数第二个成员（5）可通过WITHSCORES选项让成员和它的score值一并返回 | ZRANGE key start stop [WITHSCORES] |
| ZRANK | （1）返回有序集key中成员member的排名，有序集成员按score值从小到大排列（2）排名以0为底，即score最小的成员排名为0（3）ZREVRANK命令可将成员按score值从大到小排名 | ZRANK key number |
| ZREM | （1）移除有序集key中的一个或多个成员，不存在的成员将被忽略（2）当key存在但不是有序集时，返回错误 | ZREM key member [member ...] |
| ZREMRANGEBYRANK | （1）移除有序集key中指定排名区间内的所有成员 | ZREMRANGEBYRANK key start stop |
| ZREMRANGEBYSCORE | （1）移除有序集key中，所有score值>=min且<=max之间的成员 | ZREMRANGEBYSCORE key min max |


### 1.2 Redis 的三种特殊数据类型

- Geo：Redis3.2推出的，地理位置定位，用于存储地理位置信息，并对存储的信息进行操作。
- HyperLogLog：用来做基数统计算法的数据结构，如统计网站的UV。
- Bitmaps ：用一个比特位来映射某个元素的状态，在Redis中，它的底层是基于字符串类型实现的，可以把bitmaps成作一个以比特位为单位的数组

## 2. Redis原理

![](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1Pmpz5ftMEn1xpdGVqjkEhEQD5J2Yibo7oJhcvA4zNicsOLjIE2gLpN4fqoznnJR81H7hd0MyxkLCgnYibQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1#id=gClJI&originHeight=798&originWidth=1080&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

### 2.1 基于内存存储实现

我们都知道内存读写是比在磁盘快很多的，Redis基于内存存储实现的数据库，相对于数据存在磁盘的MySQL数据库，省去磁盘I/O的消耗。

### 2.2 高效的数据结构

我们知道，Mysql索引为了提高效率，选择了B+树的数据结构。其实合理的数据结构，就是可以让你的应用/程序更快。先看下Redis的数据结构&内部编码图：

![](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1Pmpz5ftMEn1xpdGVqjkEhEQD5yTnrltBicYLjyTWpl3g6Cdv2YJMS0EBzDRQD9KQesMAJhq9zI6lgr7w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1#id=x9HYX&originHeight=576&originWidth=1080&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

#### SDS简单动态字符串

![](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1Pmpz5ftMEn1xpdGVqjkEhEQD5FZjZIpibCML5icsrmPE5zGz6EUbyKMPRuGicibTRqQFjvCb230PPGKs7wA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1#id=lI5c7&originHeight=547&originWidth=1080&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

```
1、字符串长度处理：Redis获取字符串长度，时间复杂度为O(1)，而C语言中，需要从头开始遍历，复杂度为O（n）;
2、空间预分配：字符串修改越频繁的话，内存分配越频繁，就会消耗性能，而SDS修改和空间扩充，会额外分配未使用的空间，减少性能损耗。
3、惰性空间释放：SDS 缩短时，不是回收多余的内存空间，而是free记录下多余的空间，后续有变更，直接使用free中记录的空间，减少分配。
4、二进制安全：Redis可以存储一些二进制数据，在C语言中字符串遇到'\0'会结束，而 SDS中标志字符串结束的是len属性。
```

#### 字典

Redis 作为 K-V 型内存数据库，所有的键值就是用字典来存储。字典就是哈希表，比如HashMap，通过key就可以直接获取到对应的value。而哈希表的特性，在O（1）时间复杂度就可以获得对应的值。

#### 跳跃表

![](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1Pmpz5ftMEn1xpdGVqjkEhEQD5SicC16efExjOgXld8MROfb0JszTh0ZriaRr3ydfZpf9SDPQORkObFXsw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1#id=NRE74&originHeight=384&originWidth=904&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

```
1、跳跃表是Redis特有的数据结构，就是在链表的基础上，增加多级索引提升查找效率。
2、跳跃表支持平均 O（logN）,最坏 O（N）复杂度的节点查找，还可以通过顺序性操作批量处理节点。
```

### 2.3 合理的数据编码

Redis 支持多种数据数据类型，每种基本类型，可能对多种数据结构。什么时候,使用什么样数据结构，使用什么样编码，是redis设计者总结优化的结果。

```
String：如果存储数字的话，是用int类型的编码;如果存储非数字，小于等于39字节的字符串，是embstr；大于39个字节，则是raw编码。
List：如果列表的元素个数小于512个，列表每个元素的值都小于64字节（默认），使用ziplist编码，否则使用linkedlist编码
Hash：哈希类型元素个数小于512个，所有值小于64字节的话，使用ziplist编码,否则使用hashtable编码。
Set：如果集合中的元素都是整数且元素个数小于512个，使用intset编码，否则使用hashtable编码。
Zset：当有序集合的元素个数小于128个，每个元素的值小于64字节时，使用ziplist编码，否则使用skiplist（跳跃表）编码
```

### 2.4 合理的线程模型

**I/O 多路复用**

![](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1Pmpz5ftMEn1xpdGVqjkEhEQD5PIdxxXhX2DFlnJtL5E2pssomwYZB2ibvz0YXmjK8OpPv0jUb6GnyibxQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1#id=KD2j9&originHeight=583&originWidth=1080&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)I/O 多路复用

```
多路I/O复用技术可以让单个线程高效的处理多个连接请求，而Redis使用用epoll作为I/O多路复用技术的实现。并且，Redis自身的事件处理模型将epoll中的连接、读写、关闭都转换为事件，不在网络I/O上浪费过多的时间。
```

什么是I/O多路复用？

```
I/O ：网络 I/O
多路 ：多个网络连接
复用：复用同一个线程。
IO多路复用其实就是一种同步IO模型，它实现了一个线程可以监视多个文件句柄；一旦某个文件句柄就绪，就能够通知应用程序进行相应的读写操作；而没有文件句柄就绪时,就会阻塞应用程序，交出cpu。
```

**单线程模型**

-  Redis是单线程模型的，而单线程避免了CPU不必要的上下文切换和竞争锁的消耗。也正因为是单线程，如果某个命令执行过长（如hgetall命令），会造成阻塞。Redis是面向快速执行场景的数据库。，所以要慎用如smembers和lrange、hgetall等命令。 
-  Redis 6.0 引入了多线程提速，它的执行命令操作内存的仍然是个单线程。 

### 2.5 虚拟内存机制

Redis直接自己构建了VM机制 ，不会像一般的系统会调用系统函数处理，会浪费一定的时间去移动和请求。

**Redis的虚拟内存机制是啥呢？**

```
虚拟内存机制就是暂时把不经常访问的数据(冷数据)从内存交换到磁盘中，从而腾出宝贵的内存空间用于其它需要访问的数据(热数据)。通过VM功能可以实现冷热数据分离，使热数据仍在内存中、冷数据保存到磁盘。这样就可以避免因为内存不足而造成访问速度下降的问题。
```

## 3. 缓存击穿、缓存穿透、缓存雪崩

### 3.1 缓存穿透问题

先来看一个常见的缓存使用方式：读请求来了，先查下缓存，缓存有值命中，就直接返回；缓存没命中，就去查数据库，然后把数据库的值更新到缓存，再返回。

![](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1Pmpz5ftMEn1xpdGVqjkEhEQD5AWpGbywxZXaFZ6b5WRBy1PPcbMtGnpNIDcHa133PlLVJcIcwriaCjNA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1#id=bpwvw&originHeight=616&originWidth=395&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)读取缓存

**缓存穿透**：指查询一个一定不存在的数据，由于缓存是不命中时需要从数据库查询，查不到数据则不写入缓存，这将导致这个不存在的数据每次请求都要到数据库去查询，进而给数据库带来压力。

> 通俗点说，读请求访问时，缓存和数据库都没有某个值，这样就会导致每次对这个值的查询请求都会穿透到数据库，这就是缓存穿透。


缓存穿透一般都是这几种情况产生的：

- **业务不合理的设计**，比如大多数用户都没开守护，但是你的每个请求都去缓存，查询某个userid查询有没有守护。
- **业务/运维/开发失误的操作**，比如缓存和数据库的数据都被误删除了。
- **黑客非法请求攻击**，比如黑客故意捏造大量非法请求，以读取不存在的业务数据。

**如何避免缓存穿透呢？** 一般有三种方法。

- 1.如果是非法请求，我们在API入口，对参数进行校验，过滤非法值。
- 2.如果查询数据库为空，我们可以给缓存设置个空值，或者默认值。但是如有有写请求进来的话，需要更新缓存哈，以保证缓存一致性，同时，最后给缓存设置适当的过期时间。（业务上比较常用，简单有效）
- 3.使用布隆过滤器快速判断数据是否存在。即一个查询请求过来时，先通过布隆过滤器判断值是否存在，存在才继续往下查。

> 布隆过滤器原理：它由初始值为0的位图数组和N个哈希函数组成。一个对一个key进行N个hash算法获取N个值，在比特数组中将这N个值散列后设定为1，然后查的时候如果特定的这几个位置都为1，那么布隆过滤器判断该key存在。


### 3.2 缓存雪奔问题

**缓存雪奔：** 指缓存中数据大批量到过期时间，而查询数据量巨大，请求都直接访问数据库，引起数据库压力过大甚至down机。

- 缓存雪奔一般是由于大量数据同时过期造成的，对于这个原因，可通过均匀设置过期时间解决，即让过期时间相对离散一点。如采用一个较大固定值+一个较小的随机值，5小时+0到1800秒酱紫。
- Redis 故障宕机也可能引起缓存雪奔。这就需要构造Redis高可用集群啦。

### 3.3 缓存击穿问题

**缓存击穿：** 指热点key在某个时间点过期的时候，而恰好在这个时间点对这个Key有大量的并发请求过来，从而大量的请求打到db。

缓存击穿看着有点像，其实它两区别是，缓存雪奔是指数据库压力过大甚至down机，缓存击穿只是大量并发请求到了DB数据库层面。可以认为击穿是缓存雪奔的一个子集吧。有些文章认为它俩区别，是区别在于击穿针对某一热点key缓存，雪奔则是很多key。

解决方案就有两种：

- **1.使用互斥锁方案**。缓存失效时，不是立即去加载db数据，而是先使用某些带成功返回的原子操作命令，如(Redis的setnx）去操作，成功的时候，再去加载db数据库数据和设置缓存。否则就去重试获取缓存。
- **2. “永不过期”**，是指没有设置过期时间，但是热点数据快要过期时，异步线程去更新和设置过期时间。

## 4. Redis优化

### 4.1 什么是热Key问题，如何解决热key问题

**什么是热Key呢**？在Redis中，我们把访问频率高的key，称为热点key。

如果某一热点key的请求到服务器主机时，由于请求量特别大，可能会导致主机资源不足，甚至宕机，从而影响正常的服务。

![](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1Pmpz5ftMEn1xpdGVqjkEhEQD5L0zsXRnRD76U5wfqHQUpiaicGjWPlVT3xJzicQqzYd92rgpSBIDIiaI60Q/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1#id=C3TgB&originHeight=410&originWidth=848&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

而热点Key是怎么产生的呢？主要原因有两个：

>  
> - 用户消费的数据远大于生产的数据，如秒杀、热点新闻等读多写少的场景。
> - 请求分片集中，超过单Redi服务器的性能，比如固定名称key，Hash落入同一台服务器，瞬间访问量极大，超过机器瓶颈，产生热点Key问题。
> 
 


那么在日常开发中，如何识别到热点key呢？

>  
> - 凭经验判断哪些是热Key；
> - 客户端统计上报；
> - 服务代理层上报
> 
 


如何解决热key问题？

>  
> - Redis集群扩容：增加分片副本，均衡读流量；
> - 将热key分散到不同的服务器中；
> - 使用二级缓存，即JVM本地缓存,减少Redis的读请求。
> 
 


### 4.2 Redis 过期策略和内存淘汰策略

![](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1Pmpz5ftMEn1xpdGVqjkEhEQD5ic3Gd0rib4uZNbWvEhYDfAvPp2QjuysS921WzicTiaP0dibUhCu8xx6LJNw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1#id=HI1uv&originHeight=570&originWidth=1080&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

#### 4.2.1 Redis的过期策略

我们在`set key`的时候，可以给它设置一个过期时间，比如`expire key 60`。指定这key60s后过期，60s后，redis是如何处理的嘛？我们先来介绍几种过期策略：

##### 定时过期

> 每个设置过期时间的key都需要创建一个定时器，到过期时间就会立即对key进行清除。该策略可以立即清除过期的数据，对内存很友好；但是会占用大量的CPU资源去处理过期的数据，从而影响缓存的响应时间和吞吐量。


##### 惰性过期

> 只有当访问一个key时，才会判断该key是否已过期，过期则清除。该策略可以最大化地节省CPU资源，却对内存非常不友好。极端情况可能出现大量的过期key没有再次被访问，从而不会被清除，占用大量内存。


##### 定期过期

> 每隔一定的时间，会扫描一定数量的数据库的expires字典中一定数量的key，并清除其中已过期的key。该策略是前两者的一个折中方案。通过调整定时扫描的时间间隔和每次扫描的限定耗时，可以在不同情况下使得CPU和内存资源达到最优的平衡效果。
>  
> expires字典会保存所有设置了过期时间的key的过期时间数据，其中，key是指向键空间中的某个键的指针，value是该键的毫秒精度的UNIX时间戳表示的过期时间。键空间是指该Redis集群中保存的所有键。


Redis中同时使用了**惰性过期和定期过期**两种过期策略。

- 假设Redis当前存放30万个key，并且都设置了过期时间，如果你每隔100ms就去检查这全部的key，CPU负载会特别高，最后可能会挂掉。
- 因此，redis采取的是定期过期，每隔100ms就随机抽取一定数量的key来检查和删除的。
- 但是呢，最后可能会有很多已经过期的key没被删除。这时候，redis采用惰性删除。在你获取某个key的时候，redis会检查一下，这个key如果设置了过期时间并且已经过期了，此时就会删除。

但是呀，如果定期删除漏掉了很多过期的key，然后也没走惰性删除。就会有很多过期key积在内存内存，直接会导致内存爆的。或者有些时候，业务量大起来了，redis的key被大量使用，内存直接不够了，运维小哥哥也忘记加大内存了。难道redis直接这样挂掉？不会的！Redis用8种内存淘汰策略保护自己~

#### 4.2.2 Redis 内存淘汰策略

>  
> - volatile-lru：当内存不足以容纳新写入数据时，从设置了过期时间的key中使用LRU（最近最少使用）算法进行淘汰；
> - allkeys-lru：当内存不足以容纳新写入数据时，从所有key中使用LRU（最近最少使用）算法进行淘汰。
> - volatile-lfu：4.0版本新增，当内存不足以容纳新写入数据时，在过期的key中，使用LFU算法进行删除key。
> - allkeys-lfu：4.0版本新增，当内存不足以容纳新写入数据时，从所有key中使用LFU算法进行淘汰；
> - volatile-random：当内存不足以容纳新写入数据时，从设置了过期时间的key中，随机淘汰数据；。
> - allkeys-random：当内存不足以容纳新写入数据时，从所有key中随机淘汰数据。
> - volatile-ttl：当内存不足以容纳新写入数据时，在设置了过期时间的key中，根据过期时间进行淘汰，越早过期的优先被淘汰；
> - noeviction：默认策略，当内存不足以容纳新写入数据时，新写入操作会报错。
> 
 


### 4.3 Redis持久化

Redis是基于内存的非关系型K-V数据库，既然它是基于内存的，如果Redis服务器挂了，数据就会丢失。为了避免数据丢失了，Redis提供了**持久化**，即把数据保存到磁盘。

Redis提供了**RDB和AOF**两种持久化机制，它持久化文件加载流程如下：

![](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1Pmpz5ftMEn1xpdGVqjkEhEQD520yt6nBGgQ9oiaolDB7dv2NWjc9BnS7fUIQj1gBcKmhOJWPSPrLDv4A/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1#id=vgg5h&originHeight=963&originWidth=799&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

#### 4.3.1 RDB

**RDB**，就是把内存数据以快照的形式保存到磁盘上。

> 什么是快照?可以这样理解，给当前时刻的数据，拍一张照片，然后保存下来。


RDB持久化，是指在指定的时间间隔内，执行指定次数的写操作，将内存中的数据集快照写入磁盘中，它是Redis默认的持久化方式。执行完操作后，在指定目录下会生成一个`dump.rdb`文件，Redis 重启的时候，通过加载`dump.rdb`文件来恢复数据。RDB触发机制主要有以下几种：

![](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1Pmpz5ftMEn1xpdGVqjkEhEQD5HvsSLK1j4xibricZUFMDhR6w2B4AxZOFiapFxia5xVb66ia2GqL2L3uVz8A/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1#id=qVC0M&originHeight=210&originWidth=1080&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**RDB 的优点**

- 适合大规模的数据恢复场景，如备份，全量复制等

**RDB缺点**

- 没办法做到实时持久化/秒级持久化。
- 新老版本存在RDB格式兼容问题

**1、自动触发**

**①、save：**这里是用来配置触发 Redis的 RDB 持久化条件，比如“save m n”。表示m秒内数据集存在n次修改时，自动触发bgsave

```
save 900 1：表示900 秒内如果至少有 1 个 key 的值变化，则保存
save 300 10：表示300 秒内如果至少有 10 个 key 的值变化，则保存
save 60 10000：表示60 秒内如果至少有 10000 个 key 的值变化，则保存
```

当然如果你只是用Redis的缓存功能，不需要持久化，那么你可以注释掉所有的 save 行来停用保存功能。可以直接一个空字符串来实现停用：save ""

**②、stop-writes-on-bgsave-error ：**默认值为yes。当启用了RDB且最后一次后台保存数据失败，Redis是否停止接收数据。这会让用户意识到数据没有正确持久化到磁盘上，否则没有人会注意到灾难（disaster）发生了。如果Redis重启了，那么又可以重新开始接收数据了**③、rdbcompression ；**默认值是yes。对于存储到磁盘中的快照，可以设置是否进行压缩存储。如果是的话，redis会采用LZF算法进行压缩。如果你不想消耗CPU来进行压缩的话，可以设置为关闭此功能，但是存储在磁盘上的快照会比较大。**④、rdbchecksum ：**默认值是yes。在存储快照后，我们还可以让redis使用CRC64算法来进行数据校验，但是这样做会增加大约10%的性能消耗，如果希望获取到最大的性能提升，可以关闭此功能。

**⑤、dbfilename ：**设置快照的文件名，默认是 dump.rdb

**⑥、dir：**设置快照文件的存放路径，这个配置项一定是个目录，而不能是文件名。默认是和当前配置文件保存在同一目录。

也就是说通过在配置文件中配置的 save 方式，当实际操作满足该配置形式时就会进行 RDB 持久化，将当前的内存快照保存在 dir 配置的目录中，文件名由配置的 dbfilename 决定。

**2、手动触发**

手动触发Redis进行RDB持久化的命令有两种：

1、save

该命令会阻塞当前Redis服务器，执行save命令期间，Redis不能处理其他命令，直到RDB过程完成为止。

显然该命令对于内存比较大的实例会造成长时间阻塞，这是致命的缺陷，为了解决此问题，Redis提供了第二种方式。

2、bgsave

执行该命令时，Redis会在后台异步进行快照操作，快照同时还可以响应客户端请求。具体操作是Redis进程执行fork操作创建子进程，RDB持久化过程由子进程负责，完成后自动结束。阻塞只发生在fork阶段，一般时间很短。基本上 Redis 内部所有的RDB操作都是采用 bgsave 命令。

ps:执行执行 flushall 命令，也会产生dump.rdb文件，但里面是空的.

**3、恢复数据**：

将备份文件 (dump.rdb) 移动到 redis 安装目录并启动服务即可，redis就会自动加载文件数据至内存了。Redis 服务器在载入 RDB 文件期间，会一直处于阻塞状态，直到载入工作完成为止。

![](https://img2020.cnblogs.com/i-beta/1120165/202003/1120165-20200306220954168-293357018.png#id=wDH0p&originHeight=678&originWidth=1848&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**4、停止RDB持久化**

可以通过上面讲的在配置文件 redis.conf 中，可以注释掉所有的 save 行来停用保存功能或者直接一个空字符串来实现停用：save ""

也可以通过命令：

```
redis-cli config set save " "
```

**5、RDB 优势和劣势**

①、优势

1.RDB是一个非常紧凑(compact)的文件，它保存了redis 在某个时间点上的数据集。这种文件非常适合用于进行备份和灾难恢复。

2.生成RDB文件的时候，redis主进程会fork()一个子进程来处理所有保存工作，主进程不需要进行任何磁盘IO操作。

3.RDB 在恢复大数据集时的速度比 AOF 的恢复速度要快。

②、劣势

1、RDB方式数据没办法做到实时持久化/秒级持久化。因为bgsave每次运行都要执行fork操作创建子进程，属于重量级操作，如果不采用压缩算法(内存中的数据被克隆了一份，大致2倍的膨胀性需要考虑)，频繁执行成本过高(影响性能)

2、RDB文件使用特定二进制格式保存，Redis版本演进过程中有多个格式的RDB版本，存在老版本Redis服务无法兼容新版RDB格式的问题(版本不兼容)

3、在一定间隔时间做一次备份，所以如果redis意外down掉的话，就会丢失最后一次快照后的所有修改(数据有丢失)

**6、RDB 自动保存原理**

Redis有个服务器状态结构：

```
struct redisService{
     //1、记录保存save条件的数组
     struct saveparam *saveparams;
     //2、修改计数器
     long long dirty;
     //3、上一次执行保存的时间
     time_t lastsave;
}
```

①、首先看记录保存save条件的数组 saveparam，里面每个元素都是一个 saveparams 结构：

```
struct saveparam{
     //秒数
     time_t seconds;
     //修改数
     int changes;
};
```

前面我们在 redis.conf 配置文件中进行了关于save 的配置：

```
save 900 1：表示900 秒内如果至少有 1 个 key 的值变化，则保存
save 300 10：表示300 秒内如果至少有 10 个 key 的值变化，则保存
save 60 10000：表示60 秒内如果至少有 10000 个 key 的值变化，则保存
```

那么服务器状态中的saveparam 数组将会是如下的样子：

![](https://images2018.cnblogs.com/blog/1120165/201806/1120165-20180605205542608-719758439.png#id=Trm5H&originHeight=177&originWidth=643&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

②、dirty 计数器和lastsave 属性

dirty 计数器记录距离上一次成功执行 save 命令或者 bgsave 命令之后，Redis服务器进行了多少次修改（包括写入、删除、更新等操作）。

lastsave 属性是一个时间戳，记录上一次成功执行 save 命令或者 bgsave 命令的时间。

通过这两个命令，当服务器成功执行一次修改操作，那么dirty 计数器就会加 1，而lastsave 属性记录上一次执行save或bgsave的时间，Redis 服务器还有一个周期性操作函数 severCron ,默认每隔 100 毫秒就会执行一次，该函数会遍历并检查 saveparams 数组中的所有保存条件，只要有一个条件被满足，那么就会执行 bgsave 命令。

执行完成之后，dirty 计数器更新为 0 ，lastsave 也更新为执行命令的完成时间。

#### 4.3.2 AOF

**AOF（append only file）** 持久化，采用日志的形式来记录每个写操作，追加到文件中，重启时再重新执行AOF文件中的命令来恢复数据。它主要解决数据持久化的实时性问题。默认是不开启的。

AOF的工作流程如下：

![](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1Pmpz5ftMEn1xpdGVqjkEhEQD54UHq5Kib0p6zz48eIE6tEaXEEDgtVnkSgkaR7B3QPz76JmYP4bmmciaQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1#id=rPG0X&originHeight=582&originWidth=502&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**AOF的优点**

- 数据的一致性和完整性更高

**AOF的缺点**

- AOF记录的内容越多，文件越大，数据恢复变慢。

**1、配置**

①、**appendonly**：默认值为no，也就是说redis 默认使用的是rdb方式持久化，如果想要开启 AOF 持久化方式，需要将 appendonly 修改为 yes。

②、**appendfilename** ：aof文件名，默认是"appendonly.aof"

③、**appendfsync：**aof持久化策略的配置；

no表示不执行fsync，由操作系统保证数据同步到磁盘，速度最快，但是不太安全；

always表示每次写入都执行fsync，以保证数据同步到磁盘，效率很低；

everysec表示每秒执行一次fsync，可能会导致丢失这1s数据。通常选择 everysec ，兼顾安全性和效率。

④、**no-appendfsync-on-rewrite**：在aof重写或者写入rdb文件的时候，会执行大量IO，此时对于everysec和always的aof模式来说，执行fsync会造成阻塞过长时间，no-appendfsync-on-rewrite字段设置为默认设置为no。如果对延迟要求很高的应用，这个字段可以设置为yes，否则还是设置为no，这样对持久化特性来说这是更安全的选择。  设置为yes表示rewrite期间对新写操作不fsync,暂时存在内存中,等rewrite完成后再写入，默认为no，建议yes。Linux的默认fsync策略是30秒。可能丢失30秒数据。默认值为no。

⑤、**auto-aof-rewrite-percentage**：默认值为100。aof自动重写配置，当目前aof文件大小超过上一次重写的aof文件大小的百分之多少进行重写，即当aof文件增长到一定大小的时候，Redis能够调用bgrewriteaof对日志文件进行重写。当前AOF文件大小是上次日志重写得到AOF文件大小的二倍（设置为100）时，自动启动新的日志重写过程。

⑥、**auto-aof-rewrite-min-size**：64mb。设置允许重写的最小aof文件大小，避免了达到约定百分比但尺寸仍然很小的情况还要重写。

⑦、**aof-load-truncated**：aof文件可能在尾部是不完整的，当redis启动的时候，aof文件的数据被载入内存。重启可能发生在redis所在的主机操作系统宕机后，尤其在ext4文件系统没有加上data=ordered选项，出现这种现象 redis宕机或者异常终止不会造成尾部不完整现象，可以选择让redis退出，或者导入尽可能多的数据。如果选择的是yes，当截断的aof文件被导入的时候，会自动发布一个log给客户端然后load。如果是no，用户必须手动redis-check-aof修复AOF文件才可以。默认值为 yes。

**2、开启 AOF**

将 redis.conf 的 appendonly 配置改为 yes 即可。

**3、AOF 文件恢复**

重启 Redis 之后就会进行 AOF 文件的载入。

异常修复命令：redis-check-aof --fix 进行修复

**4、AOF重写**

由于AOF持久化是Redis不断将写命令记录到 AOF 文件中，随着Redis不断的进行，AOF 的文件会越来越大，文件越大，占用服务器内存越大以及 AOF 恢复要求时间越长。为了解决这个问题，Redis新增了重写机制，当AOF文件的大小超过所设定的阈值时，Redis就会启动AOF文件的内容压缩，只保留可以恢复数据的最小指令集。可以使用命令 bgrewriteaof 来重新。

AOF 文件重写触发机制：通过 redis.conf 配置文件中的 auto-aof-rewrite-percentage：默认值为100，以及auto-aof-rewrite-min-size：64mb 配置，也就是说默认Redis会记录上次重写时的AOF大小，**默认配置是当AOF文件大小是上次rewrite后大小的一倍且文件大于64M时触发。**

这里再提一下，我们知道 Redis 是单线程工作，如果 重写 AOF 需要比较长的时间，那么在重写 AOF 期间，Redis将长时间无法处理其他的命令，这显然是不能忍受的。Redis为了克服这个问题，解决办法是将 AOF 重写程序放到子程序中进行，这样有两个好处：

①、子进程进行 AOF 重写期间，服务器进程（父进程）可以继续处理其他命令。

②、子进程带有父进程的数据副本，使用子进程而不是线程，可以在避免使用锁的情况下，保证数据的安全性。

使用子进程解决了上面的问题，但是新问题也产生了：因为子进程在进行 AOF 重写期间，服务器进程依然在处理其它命令，这新的命令有可能也对数据库进行了修改操作，使得当前数据库状态和重写后的 AOF 文件状态不一致。

为了解决这个数据状态不一致的问题，Redis 服务器设置了一个 AOF 重写缓冲区，这个缓冲区是在创建子进程后开始使用，当Redis服务器执行一个写命令之后，就会将这个写命令也发送到 AOF 重写缓冲区。当子进程完成 AOF 重写之后，就会给父进程发送一个信号，父进程接收此信号后，就会调用函数将 AOF 重写缓冲区的内容都写到新的 AOF 文件中。

这样将 AOF 重写对服务器造成的影响降到了最低。

**5、AOF 优缺点**

优点：

①、AOF 持久化的方法提供了多种的同步频率，即使使用默认的同步频率每秒同步一次，Redis 最多也就丢失 1 秒的数据而已。

②、AOF 文件使用 Redis 命令追加的形式来构造，因此，即使 Redis 只能向 AOF 文件写入命令的片断，使用 redis-check-aof 工具也很容易修正 AOF 文件。

③、AOF 文件的格式可读性较强，这也为使用者提供了更灵活的处理方式。例如，如果我们不小心错用了 FLUSHALL 命令，在重写还没进行时，我们可以手工将最后的 FLUSHALL 命令去掉，然后再使用 AOF 来恢复数据。

缺点：

①、对于具有相同数据的的 Redis，AOF 文件通常会比 RDF 文件体积更大。

②、虽然 AOF 提供了多种同步的频率，默认情况下，每秒同步一次的频率也具有较高的性能。但在 Redis 的负载较高时，RDB 比 AOF 具好更好的性能保证。

③、RDB 使用快照的形式来持久化整个 Redis 数据，而 AOF 只是将每次执行的命令追加到 AOF 文件中，因此从理论上说，RDB 比 AOF 方式更健壮。官方文档也指出，AOF 的确也存在一些 BUG，这些 BUG 在 RDB 没有存在。

那么对于 AOF 和 RDB 两种持久化方式，我们应该如何选择呢？

如果可以忍受一小段时间内数据的丢失，毫无疑问使用 RDB 是最好的，定时生成 RDB 快照（snapshot）非常便于进行数据库备份， 并且 RDB 恢复数据集的速度也要比 AOF 恢复的速度要快，而且使用 RDB 还可以避免 AOF 一些隐藏的 bug；否则就使用 AOF 重写。但是一般情况下建议不要单独使用某一种持久化机制，而是应该两种一起用，在这种情况下,当redis重启的时候会优先载入AOF文件来恢复原始的数据，因为在通常情况下AOF文件保存的数据集要比RDB文件保存的数据集要完整。Redis后期官方可能都有将两种持久化方式整合为一种持久化模型。

**6、RDB-AOF混合持久化**

具体配置为：

```
aof-use-rdb-preamble
```

设置为yes表示开启，设置为no表示禁用。

当开启混合持久化时，主进程先fork出子进程将现有内存副本全量以RDB方式写入aof文件中，然后将缓冲区中的增量命令以AOF方式写入aof文件中，写入完成后通知主进程更新相关信息，并将新的含有 RDB和AOF两种格式的aof文件替换旧的aof文件。

简单来说：混合持久化方式产生的文件一部分是RDB格式，一部分是AOF格式。

### 4.4 Redis高可用

我们在项目中使用Redis，肯定不会是单点部署Redis服务的。因为，单点部署一旦宕机，就不可用了。为了实现高可用，通常的做法是，将数据库复制多个副本以部署在不同的服务器上，其中一台挂了也可以继续提供服务。Redis 实现高可用有三种部署模式：**主从模式，哨兵模式，集群模式**。

#### 4.4.1 主从模式

##### 主从原理

主从模式中，Redis部署了多台机器，有主节点，负责读写操作，有从节点，只负责读操作。从节点的数据来自主节点，实现原理就是**主从复制机制**

主从复制包括全量复制，增量复制两种。一般当slave第一次启动连接master，或者认为是第一次连接，就采用**全量复制**，全量复制流程如下：

![](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1Pmpz5ftMEn1xpdGVqjkEhEQD5SxdpZw0Qnn9Qzeicv6jIkAyNw4Vzhrmia8aDAkaib6chJXCialYJVYNjIA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1#id=X1ush&originHeight=853&originWidth=1080&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

- 1.slave发送sync命令到master。
- 2.master接收到SYNC命令后，执行bgsave命令，生成RDB全量文件。
- 3.master使用缓冲区，记录RDB快照生成期间的所有写命令。
- 4.master执行完bgsave后，向所有slave发送RDB快照文件。
- 5.slave收到RDB快照文件后，载入、解析收到的快照。
- 6.master使用缓冲区，记录RDB同步期间生成的所有写的命令。
- 7.master快照发送完毕后，开始向slave发送缓冲区中的写命令;
- 8.salve接受命令请求，并执行来自master缓冲区的写命令

redis2.8版本之后，已经使用**psync来替代sync**，因为sync命令非常消耗系统资源，psync的效率更高。

slave与master全量同步之后，master上的数据，如果再次发生更新，就会触发**增量复制**。

当master节点发生数据增减时，就会触发`replicationFeedSalves()`函数，接下来在 Master节点上调用的每一个命令会使用`replicationFeedSlaves()`来同步到Slave节点。执行此函数之前呢，master节点会判断用户执行的命令是否有数据更新，如果有数据更新的话，并且slave节点不为空，就会执行此函数。这个函数作用就是：**把用户执行的命令发送到所有的slave节点**，让slave节点执行。流程如下：

![](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1Pmpz5ftMEn1xpdGVqjkEhEQD5aoiaCJlhxolg7qqlJus6ezasgISeOFRoppY1DiaUDnhfw9UjicKRSRRXg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1#id=nBZJu&originHeight=872&originWidth=663&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

##### 基础配置

**1、配置文件**

首先将redis.conf 配置文件复制三份，通过修改端口分别模拟三台Redis服务器，然后我们分别对这三个redis.conf 文件进行修改。

**1)、daemonize yes **  表示指定Redis以守护进程的方式启动（后台启动）

**2)、配置PID文件路径pidfile**

![](https://images2018.cnblogs.com/blog/1120165/201806/1120165-20180609092154310-1305076236.png#id=fwJ2J&originHeight=73&originWidth=660&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

表示当redis作为守护进程运行的时候，它会把 pid 默认写到 /var/redis/run/redis_6379.pid 文件里面

**3)、配置端口port**

![](https://images2018.cnblogs.com/blog/1120165/201806/1120165-20180609092445295-1994256190.png#id=SWhWj&originHeight=69&originWidth=683&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**4)、配置log文件**

![](https://images2018.cnblogs.com/blog/1120165/201806/1120165-20180609092701160-1155649756.png#id=dWjGT&originHeight=94&originWidth=655&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**5)、配置rdb文件**

![](https://images2018.cnblogs.com/blog/1120165/201806/1120165-20180609092830634-1425851330.png#id=PzPmg&originHeight=49&originWidth=325&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

依次将 6380redis.conf 、6381redis.conf 配置一次，则配置完毕。

接下来我们分别启动这三个服务。

![](https://images2018.cnblogs.com/blog/1120165/201806/1120165-20180609093944771-2019394726.png#id=XONA7&originHeight=60&originWidth=460&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

通过命令查看Redis是否启动：

![](https://images2018.cnblogs.com/blog/1120165/201806/1120165-20180609094017041-86471770.png#id=kDaJp&originHeight=99&originWidth=711&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

接下来通过如下命令分别进入到这三个Redis客户端：

```
redis-cli -p 6379
redis-cli -p 6380
redis-cli -p 6381
```

注意:如果修改端口了,启动redis-cli 命令,比如加上 -p 端口,否则连接不上.如果密码也修改了,则还得添加密码 -a 密码指令来连接.

**6、设置主从关系**

①、通过  info replication 命令查看节点角色

![](https://images2018.cnblogs.com/blog/1120165/201806/1120165-20180609101412335-153915154.png#id=EIrW7&originHeight=252&originWidth=506&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

![](https://images2018.cnblogs.com/blog/1120165/201806/1120165-20180609101437538-230419038.png#id=TMlqn&originHeight=250&originWidth=502&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

②、选择6380端口和6381端口，执行命令：SLAVEOF 127.0.0.1 6379

![](https://images2018.cnblogs.com/blog/1120165/201806/1120165-20180609101856672-864209230.png#id=OGHPc&originHeight=446&originWidth=503&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

这里通过命令来设置主从关系，一旦服务重启，那么角色关系将不复存在。想要永久的保存这种关系，可以通过配置redis.conf 文件来配置。

```
slaveof 127.0.0.1 6379
```

#### 4.4.2 哨兵模式

主从模式中，一旦主节点由于故障不能提供服务，需要人工将从节点晋升为主节点，同时还要通知应用方更新主节点地址。显然，多数业务场景都不能接受这种故障处理方式。Redis从2.8开始正式提供了Redis Sentinel（哨兵）架构来解决这个问题。

**哨兵模式**，由一个或多个Sentinel实例组成的Sentinel系统，它可以监视所有的Redis主节点和从节点，并在被监视的主节点进入下线状态时，**自动将下线主服务器属下的某个从节点升级为新的主节点**。但是呢，一个哨兵进程对Redis节点进行监控，就可能会出现问题（**单点问题**），因此，可以使用多个哨兵来进行监控Redis节点，并且各个哨兵之间还会进行监控。

![](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1Pmpz5ftMEn1xpdGVqjkEhEQD5iaw7HOfricwXCBFgDgliaiaD0PAeqVrrkxrRNvr8lZqSl7T9vZsicDqrriaw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1#id=CSkzC&originHeight=715&originWidth=1080&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)Sentinel哨兵模式

简单来说，哨兵模式就三个作用：

- 发送命令，等待Redis服务器（包括主服务器和从服务器）返回监控其运行状态；
- 哨兵监测到主节点宕机，会自动将从节点切换成主节点，然后通过发布订阅模式通知其他的从节点，修改配置文件，让它们切换主机；
- 哨兵之间还会相互监控，从而达到高可用。

**故障切换的过程是怎样的呢**

> 假设主服务器宕机，哨兵1先检测到这个结果，系统并不会马上进行 failover 过程，仅仅是哨兵1主观的认为主服务器不可用，这个现象成为主观下线。当后面的哨兵也检测到主服务器不可用，并且数量达到一定值时，那么哨兵之间就会进行一次投票，投票的结果由一个哨兵发起，进行 failover 操作。切换成功后，就会通过发布订阅模式，让各个哨兵把自己监控的从服务器实现切换主机，这个过程称为客观下线。这样对于客户端而言，一切都是透明的。


哨兵的工作模式如下：

1. 每个Sentinel以每秒钟一次的频率向它所知的Master，Slave以及其他Sentinel实例发送一个 PING命令。
2. 如果一个实例（instance）距离最后一次有效回复 PING 命令的时间超过 down-after-milliseconds 选项所指定的值， 则这个实例会被 Sentinel标记为主观下线。
3. 如果一个Master被标记为主观下线，则正在监视这个Master的所有 Sentinel 要以每秒一次的频率确认Master的确进入了主观下线状态。
4. 当有足够数量的 Sentinel（大于等于配置文件指定的值）在指定的时间范围内确认Master的确进入了主观下线状态， 则Master会被标记为客观下线。
5. 在一般情况下， 每个 Sentinel 会以每10秒一次的频率向它已知的所有Master，Slave发送 INFO 命令。
6. 当Master被 Sentinel 标记为客观下线时，Sentinel 向下线的 Master 的所有 Slave 发送 INFO 命令的频率会从 10 秒一次改为每秒一次
7. 若没有足够数量的 Sentinel同意Master已经下线， Master的客观下线状态就会被移除；若Master 重新向 Sentinel 的 PING 命令返回有效回复， Master 的主观下线状态就会被移除。

#### 4.4.3 Cluster集群模式

哨兵模式基于主从模式，实现读写分离，它还可以自动切换，系统可用性更高。但是它每个节点存储的数据是一样的，浪费内存，并且不好在线扩容。因此，Cluster集群应运而生，它在Redis3.0加入的，实现了Redis的**分布式存储**。对数据进行分片，也就是说**每台Redis节点上存储不同的内容**，来解决在线扩容的问题。并且，它也提供复制和故障转移的功能。

##### Cluster集群节点的通讯

一个Redis集群由多个节点组成，**各个节点之间是怎么通信的呢**？通过**Gossip协议**！

Redis Cluster集群通过Gossip协议进行通信，节点之前不断交换信息，交换的信息内容包括节点出现故障、新节点加入、主从节点变更信息、slot信息等等。常用的Gossip消息分为4种，分别是：ping、pong、meet、fail。

![](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1Pmpz5ftMEn1xpdGVqjkEhEQD5EnicZdibKdbO6KMWeEo4ejqE7JcylX209wv4Ao98sKPoNQnXtPqNA3sg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1#id=ay6T0&originHeight=634&originWidth=1080&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

>  
> - meet消息：通知新节点加入。消息发送者通知接收者加入到当前集群，meet消息通信正常完成后，接收节点会加入到集群中并进行周期性的ping、pong消息交换。
> - ping消息：集群内交换最频繁的消息，集群内每个节点每秒向多个其他节点发送ping消息，用于检测节点是否在线和交换彼此状态信息。
> - pong消息：当接收到ping、meet消息时，作为响应消息回复给发送方确认消息正常通信。pong消息内部封装了自身状态数据。节点也可以向集群内广播自身的pong消息来通知整个集群对自身状态进行更新。
> - fail消息：当节点判定集群内另一个节点下线时，会向集群内广播一个fail消息，其他节点接收到fail消息之后把对应节点更新为下线状态。
> 
 


特别的，每个节点是通过**集群总线(cluster bus)** 与其他的节点进行通信的。通讯时，使用特殊的端口号，即对外服务端口号加10000。例如如果某个node的端口号是6379，那么它与其它nodes通信的端口号是 16379。nodes 之间的通信采用特殊的二进制协议。

##### Hash Slot插槽算法

既然是分布式存储，Cluster集群使用的分布式算法是**一致性Hash**嘛？并不是，而是**Hash Slot插槽算法**。

**插槽算法**把整个数据库被分为16384个slot（槽），每个进入Redis的键值对，根据key进行散列，分配到这16384插槽中的一个。使用的哈希映射也比较简单，用CRC16算法计算出一个16 位的值，再对16384取模。数据库中的每个键都属于这16384个槽的其中一个，集群中的每个节点都可以处理这16384个槽。

集群中的每个节点负责一部分的hash槽，比如当前集群有A、B、C个节点，每个节点上的哈希槽数 =16384/3，那么就有：

- 节点A负责0~5460号哈希槽
- 节点B负责5461~10922号哈希槽
- 节点C负责10923~16383号哈希槽

##### Redis Cluster集群

Redis Cluster集群中，需要确保16384个槽对应的node都正常工作，如果某个node出现故障，它负责的slot也会失效，整个集群将不能工作。

因此为了保证高可用，Cluster集群引入了主从复制，一个主节点对应一个或者多个从节点。当其它主节点 ping 一个主节点 A 时，如果半数以上的主节点与 A 通信超时，那么认为主节点 A 宕机了。如果主节点宕机时，就会启用从节点。

在Redis的每一个节点上，都有两个玩意，一个是插槽（slot），它的取值范围是0~16383。另外一个是cluster，可以理解为一个集群管理的插件。当我们存取的key到达时，Redis 会根据CRC16算法得出一个16 bit的值，然后把结果对16384取模。酱紫每个key都会对应一个编号在 0~16383 之间的哈希槽，通过这个值，去找到对应的插槽所对应的节点，然后直接自动跳转到这个对应的节点上进行存取操作。

虽然数据是分开存储在不同节点上的，但是对客户端来说，整个集群Cluster，被看做一个整体。客户端端连接任意一个node，看起来跟操作单实例的Redis一样。当客户端操作的key没有被分配到正确的node节点时，Redis会返回转向指令，最后指向正确的node，这就有点像浏览器页面的302 重定向跳转。

![](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1Pmpz5ftMEn1xpdGVqjkEhEQD5s2tZjl6m6g3GTayICkFvbsJXQpFQ3P00bXkp8arzL7RlTHhcLD6G3A/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1#id=bDBKS&originHeight=646&originWidth=893&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

##### 故障转移

Redis集群实现了高可用，当集群内节点出现故障时，通过**故障转移**，以保证集群正常对外提供服务。

redis集群通过ping/pong消息，实现故障发现。这个环境包括**主观下线和客观下线**。

**主观下线：** 某个节点认为另一个节点不可用，即下线状态，这个状态并不是最终的故障判定，只能代表一个节点的意见，可能存在误判情况。

![](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1Pmpz5ftMEn1xpdGVqjkEhEQD5K1Fn1GfOxnPwicYiaxywwibkHxopiciaqWUDPIAjbVfD6AtXiaoWxtmibCS8Q/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1#id=r7C90&originHeight=590&originWidth=1080&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)主观下线

**客观下线：** 指标记一个节点真正的下线，集群内多个节点都认为该节点不可用，从而达成共识的结果。如果是持有槽的主节点故障，需要为该节点进行故障转移。

- 假如节点A标记节点B为主观下线，一段时间后，节点A通过消息把节点B的状态发到其它节点，当节点C接受到消息并解析出消息体时，如果发现节点B的pfail状态时，会触发客观下线流程；
- 当下线为主节点时，此时Redis Cluster集群为统计持有槽的主节点投票，看投票数是否达到一半，当下线报告统计数大于一半时，被标记为**客观下线**状态。

流程如下：

![](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1Pmpz5ftMEn1xpdGVqjkEhEQD57IuSiaVBOicKRKsfH0NfXgJiaTSaibZlQf7NBReBmYelEHklRn5dB1icGRQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1#id=XyN7B&originHeight=623&originWidth=1080&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)客观下线

**故障恢复**：故障发现后，如果下线节点的是主节点，则需要在它的从节点中选一个替换它，以保证集群的高可用。流程如下：

![](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1Pmpz5ftMEn1xpdGVqjkEhEQD5kCjyoezpdPVttGrMZjKU4CxLISenrRAmL4WUSVfM4flC2vBZSYs6Ng/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1#id=Zem08&originHeight=741&originWidth=547&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

- 资格检查：检查从节点是否具备替换故障主节点的条件。
- 准备选举时间：资格检查通过后，更新触发故障选举时间。
- 发起选举：到了故障选举时间，进行选举。
- 选举投票：只有持有槽的**主节点**才有票，从节点收集到足够的选票（大于一半），触发**替换主节点操作**

##### 基础配置

![](https://img2018.cnblogs.com/i-beta/1120165/202002/1120165-20200220194010583-1216911180.png#id=LUpOv&originHeight=389&originWidth=918&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**1、配置文件：**

```
#配置端口
port ${port}
#以守护进程模式启动
daemonize yes
#pid的存放文件
pidfile /var/run/redis_${port}.pid
#日志文件名
logfile "redis_${port}.log"
#存放备份文件以及日志等文件的目录
dir "/opt/redis/data"  
#rdb备份文件名
dbfilename "dump_${port}.rdb"
#开启集群功能
cluster-enabled yes
#集群配置文件，节点自动维护
cluster-config-file nodes-${port}.conf
#集群能够运行不需要集群中所有节点都是成功的
cluster-require-full-coverage no
```

配置完成后，通过 redis-server redis.conf 命令启动这六个节点。

启动之后，进程后面会有 cluster 的字样：

![](https://img2018.cnblogs.com/i-beta/1120165/202002/1120165-20200220202304911-776847031.png#id=ElESP&originHeight=71&originWidth=895&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**2、配置节点**

这里有 6 个节点，我们只需要拉通 1 个节点和另外 5 个节点之间通信，那么每两个节点就能够通信了。命令如下：

```
redis-cli -h -p ${port1} -a ${password} cluster meet ${ip2}  ${port2}
```

这里的 -a 参数表示该Redis节点有密码，如果没有可以不用加此参数。

实例中的 6 个节点，分别进行如下命令：

```
redis-cli -p 6379 -a 123 cluster meet 192.168.14.101 6382
redis-cli -p 6379 -a 123 cluster meet 192.168.14.102 6380
redis-cli -p 6379 -a 123 cluster meet 192.168.14.102 6383
redis-cli -p 6379 -a 123 cluster meet 192.168.14.103 6381
redis-cli -p 6379 -a 123 cluster meet 192.168.14.103 6384
```

执行完毕后，可以查看节点通信信息：

```
redis-cli -p 6379 -a 123 cluster nodes
```

![](https://img2018.cnblogs.com/i-beta/1120165/202002/1120165-20200220204303934-1662569257.png#id=x3SON&originHeight=182&originWidth=1237&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

结果如下：

![](https://img2018.cnblogs.com/i-beta/1120165/202002/1120165-20200220204409071-26441393.png#id=YD2k4&originHeight=428&originWidth=1179&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**3、分配插槽**

分配槽位的命令如下：

```
redis-cli -p ${port} -a ${password} cluster addslots {${startSlot}..${endSlot}}
```

如：

```
redis-cli -p 6379 -a 123 cluster addslots {0..5462}
```

**4、主从配置**

命令如下：

```
redis-cli -p ${port} -a {password} cluster replicate ${nodeId}
```

前面的${port} 表示从节点的端口，这里的nodeId表示主节点的nodeId，如下：

![](https://img2018.cnblogs.com/i-beta/1120165/202002/1120165-20200220213659345-738320038.png#id=aV0i9&originHeight=187&originWidth=1315&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

如果弄反了，会报如下错误：(error) ERR To set a master the node must be empty and without assigned slots.

**5、集群扩容**

1)、新增节点加入集群

命令如下：

```
redis-cli -p existing_port -a ${password} --cluster add-node new_host:new_port existing_host:existing_port
```

将新增的主节点 6390 添加到原来的集群中

```
redis-cli -p 6379 -a 123 --cluster add-node 192.168.14.101:6390 192.168.14.101:6379
```

2）、新增主节点分配槽位

分配命令如下：

```
redis-cli -p existing_port -a ${password} --cluster reshard existing_host:existing_port
```

后面的existing_host:existing_port表示原来集群中的任意一个节点，这个命令表示将源节点的一部分槽位分配个新增的节点。

在分配过程中，会出现如下几个提示：

```
#后面的2000表示分配2000个槽位给新增节点
How many slots do you want to move (from 1 to 16384)? 2000
#表示接受节点的NodeId,填新增节点6390的
What is the receiving node ID? 64a0779c7baef78c8fd0f2bb6e73f29375e00133d
#这里填槽的来源，要么填all，表示所有master节点都拿出一部分槽位分配给新增节点；
#要么填某个原有NodeId，表示这个节点拿出一部分槽位给新增节点
Please enter all the source node IDs.
Type 'all' to use all the nodes as source nodes for the hash slots.
Type 'done' once you entered all the source nodes IDs.
Source node #1: all
```

3）、新增从节点添加到集群

```
redis-cli -p 6379 -a 123 --cluster add-node 192.168.14.101:6391 192.168.14.101:6379
```

4）、建立新增节点主从关系

命令如下：

```
redis-cli -p ${port} -a {password} cluster replicate ${nodeId}
```

**6、集群收缩**

1）、迁移待移除节点槽位

```
redis-cli -p existing_port -a {Redis登录密码} --cluster reshard --cluster-from {待移除的NodeId} --cluster-to {接受移除节点的NodeId} --cluster-slots {移除的槽位个数} existing_host:existing_port
```

比如，我这里要移除主节点 6390 的所有槽位，给6379节点。

```
redis-cli -p 6379 -a 123 --cluster reshard --cluster-from 4a0779c7baef78c8fd0f2bb6e73f29375e00133d --cluster-to 001a22b1edae6ea1699b753d193871824723f375 --cluster-slots 2000 192.168.14.101:6379
```

2）、移除待删除主从节点

```
redis-cli -p existing_port -a {Redis登录密码} --cluster del-node host:port {待删除的NodeId}
```

删除 6391 从节点：

```
redis-cli -p 6379 -a 123 --cluster del-node 192.168.14.101:6379 3622ec34956b624358722e6f4a2b762574d35bf0
```

### 4.5 Redis分布式锁

**分布式锁**，是控制分布式系统不同进程共同访问共享资源的一种锁的实现。秒杀下单、抢红包等等业务场景，都需要用到分布式锁，我们项目中经常使用Redis作为分布式锁。

选了Redis分布式锁的几种实现方法，大家来讨论下，看有没有啥问题哈。

- 命令setnx + expire分开写
- setnx + value值是过期时间
- set的扩展命令（set ex px nx）
- set ex px nx + 校验唯一随机值,再删除

#### 4.5.1 命令setnx + expire分开写

```java
if（jedis.setnx(key,lock_value) == 1）{ //加锁
    expire（key，100）; //设置过期时间
    try {
        do something  //业务请求
    }catch(){
  }
  finally {
       jedis.del(key); //释放锁
    }
}
```

如果执行完`setnx`加锁，正要执行expire设置过期时间时，进程crash掉或者要重启维护了，那这个锁就“长生不老”了，**别的线程永远获取不到锁**啦，所以分布式锁**不能**这么实现。

#### 4.5.2 setnx + value值是过期时间

```java
long expires = System.currentTimeMillis() + expireTime; //系统时间+设置的过期时间
String expiresStr = String.valueOf(expires);

// 如果当前锁不存在，返回加锁成功
if (jedis.setnx(key, expiresStr) == 1) {
        return true;
} 
// 如果锁已经存在，获取锁的过期时间
String currentValueStr = jedis.get(key);

// 如果获取到的过期时间，小于系统当前时间，表示已经过期
if (currentValueStr != null && Long.parseLong(currentValueStr) < System.currentTimeMillis()) {

     // 锁已过期，获取上一个锁的过期时间，并设置现在锁的过期时间（不了解redis的getSet命令的小伙伴，可以去官网看下哈）
    String oldValueStr = jedis.getSet(key_resource_id, expiresStr);
    
    if (oldValueStr != null && oldValueStr.equals(currentValueStr)) {
         // 考虑多线程并发的情况，只有一个线程的设置值和当前值相同，它才可以加锁
         return true;
    }
}
        
//其他情况，均返回加锁失败
return false;
}
```

笔者看过有开发小伙伴是这么实现分布式锁的，但是这种方案也有这些**缺点**：

- 过期时间是客户端自己生成的，分布式环境下，每个客户端的时间必须同步。
- 没有保存持有者的唯一标识，可能被别的客户端释放/解锁。
- 锁过期的时候，并发多个客户端同时请求过来，都执行了`jedis.getSet()`，最终只能有一个客户端加锁成功，但是该客户端锁的过期时间，可能被别的客户端覆盖。

#### 4.5.3：set的扩展命令（set ex px nx）（注意可能存在的问题）

```java
if（jedis.set(key, lock_value, "NX", "EX", 100s) == 1）{ //加锁
    try {
        do something  //业务处理
    }catch(){
  }
  finally {
       jedis.del(key); //释放锁
    }
}
```

这个方案可能存在这样的问题：

- 锁过期释放了，业务还没执行完。
- 锁被别的线程误删。

#### 4.5.4 set ex px nx + 校验唯一随机值,再删除

```java
if（jedis.set(key, uni_request_id, "NX", "EX", 100s) == 1）{ //加锁
    try {
        do something  //业务处理
    }catch(){
  }
  finally {
       //判断是不是当前线程加的锁,是才释放
       if (uni_request_id.equals(jedis.get(key))) {
        jedis.del(key); //释放锁
        }
    }
}
```

在这里，判断当前线程加的锁和释放锁是不是一个原子操作。如果调用jedis.del()释放锁的时候，可能这把锁已经不属于当前客户端，**会解除他人加的锁**。

![](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1Pmpz5ftMEn1xpdGVqjkEhEQD5UFMM2oAbAzibakdjEEaooeBpvDicHic3ibQKmUbe4BibUGdbX4SBiaOF1hkQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1#id=WnQyt&originHeight=445&originWidth=1003&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

一般也是用**lua脚本**代替。lua脚本如下：

```java
if redis.call('get',KEYS[1]) == ARGV[1] then 
   return redis.call('del',KEYS[1]) 
else
   return 0
end;
```

这种方式比较不错了，一般情况下，已经可以使用这种实现方式。但是存在**锁过期释放了，业务还没执行完的问题**（实际上，估算个业务处理的时间，一般没啥问题了）。

### 4.6 Redlock算法

Redis一般都是集群部署的，假设数据在主从同步过程，主节点挂了，Redis分布式锁可能会有**哪些问题**呢？一起来看些这个流程图：

![](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1Pmpz5ftMEn1xpdGVqjkEhEQD5Y0ltgVjZhd8fQlmp6nHhGfzbLUD6plxlGIWXibjCFXhKP7XT83p8LcQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1#id=AVCeS&originHeight=397&originWidth=1062&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

如果线程一在Redis的master节点上拿到了锁，但是加锁的key还没同步到slave节点。恰好这时，master节点发生故障，一个slave节点就会升级为master节点。线程二就可以获取同个key的锁啦，但线程一也已经拿到锁了，锁的安全性就没了。

为了解决这个问题，Redis作者 antirez提出一种高级的分布式锁算法：**Redlock**。Redlock核心思想是这样的：

> 搞多个Redis master部署，以保证它们不会同时宕掉。并且这些master节点是完全相互独立的，相互之间不存在数据同步。同时，需要确保在这多个master实例上，是与在Redis单实例，使用相同方法来获取和释放锁。


我们假设当前有5个Redis master节点，在5台服务器上面运行这些Redis实例。

![](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1Pmpz5ftMEn1xpdGVqjkEhEQD5OLbOVSibd7k7iadJ0Z7lmeeMDlqb1oCJMraJibEH7PnvtibjRyuyrpicjfg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1#id=lI95Q&originHeight=738&originWidth=1080&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

RedLock的实现步骤:如下

>  
> - 1.获取当前时间，以毫秒为单位。
> - 2.按顺序向5个master节点请求加锁。客户端设置网络连接和响应超时时间，并且超时时间要小于锁的失效时间。（假设锁自动失效时间为10秒，则超时时间一般在5-50毫秒之间,我们就假设超时时间是50ms吧）。如果超时，跳过该master节点，尽快去尝试下一个master节点。
> - 3.客户端使用当前时间减去开始获取锁时间（即步骤1记录的时间），得到获取锁使用的时间。当且仅当超过一半（N/2+1，这里是5/2+1=3个节点）的Redis master节点都获得锁，并且使用的时间小于锁失效时间时，锁才算获取成功。（如上图，10s> 30ms+40ms+50ms+4m0s+50ms）
> - 如果取到了锁，key的真正有效时间就变啦，需要减去获取锁所使用的时间。
> - 如果获取锁失败（没有在至少N/2+1个master实例取到锁，有或者获取锁时间已经超过了有效时间），客户端要在所有的master节点上解锁（即便有些master节点根本就没有加锁成功，也需要解锁，以防止有些漏网之鱼）。
> 
 


简化下步骤就是：

- 按顺序向5个master节点请求加锁
- 根据设置的超时时间来判断，是不是要跳过该master节点。
- 如果大于等于三个节点加锁成功，并且使用的时间小于锁的有效期，即可认定加锁成功啦。
- 如果获取锁失败，解锁！

### 4.7 Redis的跳跃表

![](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1Pmpz5ftMEn1xpdGVqjkEhEQD5SicC16efExjOgXld8MROfb0JszTh0ZriaRr3ydfZpf9SDPQORkObFXsw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1#id=ZKhY9&originHeight=384&originWidth=904&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)跳跃表

- 跳跃表是有序集合zset的底层实现之一
- 跳跃表支持平均**O（logN）**,最坏 O（N）复杂度的节点查找，还可以通过顺序性操作批量处理节点。
- 跳跃表实现由**zskiplist和zskiplistNode**两个结构组成，其中zskiplist用于保存跳跃表信息（如表头节点、表尾节点、长度），而zskiplistNode则用于表示跳跃表节点。
- 跳跃表就是在链表的基础上，增加多级索引提升查找效率。

### 4.8 MySQL与Redis 如何保证双写一致性

- 缓存延时双删
- 删除缓存重试机制
- 读取biglog异步删除缓存

#### 4.8.1 延时双删？

什么是延时双删呢？流程图如下：

![](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1Pmpz5ftMEn1xpdGVqjkEhEQD5CIXjmnFib4NuR6sSW5yC0XIFkXa1eKiaxzZjZ49m4iadvJOzNKrI76jlg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1#id=vypZc&originHeight=592&originWidth=370&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)延时双删流程

1. 先删除缓存
2. 再更新数据库
3. 休眠一会（比如1秒），再次删除缓存。

这个休眠一会，一般多久呢？都是1秒？

> 这个休眠时间 =  读业务逻辑数据的耗时 + 几百毫秒。为了确保读请求结束，写请求可以删除读请求可能带来的缓存脏数据。


这种方案还算可以，只有休眠那一会（比如就那1秒），可能有脏数据，一般业务也会接受的。但是如果**第二次删除缓存失败**呢？缓存和数据库的数据还是可能不一致，对吧？给Key设置一个自然的expire过期时间，让它自动过期怎样？那业务要接受过期时间内，数据的不一致咯？还是有其他更佳方案呢？

#### 4.8.2 删除缓存重试机制

因为延时双删可能会存在第二步的删除缓存失败，导致的数据不一致问题。可以使用这个方案优化：删除失败就多删除几次呀,保证删除缓存成功就可以了呀~ 所以可以引入删除缓存重试机制

![](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1Pmpz5ftMEn1xpdGVqjkEhEQD5SmE94lVHArc3PAUUCt4tpASsurgFGeR4e5n0LMteSXnZGc8Wah4BDQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1#id=W4dq0&originHeight=596&originWidth=1080&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)删除缓存重试流程

1. 写请求更新数据库
2. 缓存因为某些原因，删除失败
3. 把删除失败的key放到消息队列
4. 消费消息队列的消息，获取要删除的key
5. 重试删除缓存操作

#### 4.8.3 读取biglog异步删除缓存

重试删除缓存机制还可以吧，就是会造成好多**业务代码入侵**。其实，还可以这样优化：通过数据库的binlog来异步淘汰key。

![](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1Pmpz5ftMEn1xpdGVqjkEhEQD5iabHQfRzWyXDXYUsUucQsibZAj3G3azRotMT9TjtlkibpleqtISmgLqVA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1#id=GrTWq&originHeight=895&originWidth=1080&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

以mysql为例吧

- 可以使用阿里的canal将binlog日志采集发送到MQ队列里面
- 然后通过ACK机制确认处理这条更新消息，删除缓存，保证数据缓存一致性

### 4.9 为什么Redis 6.0 之后改多线程呢？

- Redis6.0之前，Redis在处理客户端的请求时，包括读socket、解析、执行、写socket等都由一个顺序串行的主线程处理，这就是所谓的“单线程”。
- Redis6.0之前为什么一直不使用多线程？使用Redis时，几乎不存在CPU成为瓶颈的情况， Redis主要受限于内存和网络。例如在一个普通的Linux系统上，Redis通过使用pipelining每秒可以处理100万个请求，所以如果应用程序主要使用O(N)或O(log(N))的命令，它几乎不会占用太多CPU。

redis使用多线程并非是完全摒弃单线程，redis还是使用单线程模型来处理客户端的请求，只是使用多线程来处理数据的读写和协议解析，执行命令还是使用单线程。

这样做的目的是因为redis的性能瓶颈在于网络IO而非CPU，使用多线程能提升IO读写的效率，从而整体提高redis的性能。

### 4.10 Redis 事务机制

Redis通过**MULTI、EXEC、WATCH**等一组命令集合，来实现事务机制。事务支持一次执行多个命令，一个事务中所有命令都会被序列化。在事务执行过程，会按照顺序串行化执行队列中的命令，其他客户端提交的命令请求不会插入到事务执行命令序列中。

简言之，Redis事务就是**顺序性、一次性、排他性**的执行一个队列中的一系列命令。

Redis执行事务的流程如下：

- 开始事务（MULTI）
- 命令入队
- 执行事务（EXEC）、撤销事务（DISCARD ）
| 命令 | 描述 |
| --- | --- |
| EXEC | 执行所有事务块内的命令 |
| DISCARD | 取消事务，放弃执行事务块内的所有命令 |
| MULTI | 标记一个事务块的开始 |
| UNWATCH | 取消 WATCH 命令对所有 key 的监视。 |
| WATCH | 监视key ，如果在事务执行之前，该key 被其他命令所改动，那么事务将被打断。 |


### 4.11 Redis的Hash 冲突怎么办

Redis 作为一个K-V的内存数据库，它使用用一张全局的哈希来保存所有的键值对。这张哈希表，有多个哈希桶组成，哈希桶中的entry元素保存了_key和_value指针，其中_key指向了实际的键，_value指向了实际的值。![](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1Pmpz5ftMEn1xpdGVqjkEhEQD5AbZs6icDYQkibOo7paY29libaP6cLBvwYCHEOIYTLjBZDsiaeESp1gLKtw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1#id=QZpV4&originHeight=592&originWidth=1080&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

哈希表查找速率很快的，有点类似于Java中的HashMap，它让我们在O(1) 的时间复杂度快速找到键值对。首先通过key计算哈希值，找到对应的哈希桶位置，然后定位到entry，在entry找到对应的数据。

**什么是哈希冲突？**

> 哈希冲突：通过不同的key，计算出一样的哈希值，导致落在同一个哈希桶中。


Redis为了解决哈希冲突，采用了**链式哈希**。链式哈希是指同一个哈希桶中，多个元素用一个链表来保存，它们之间依次用指针连接。

![](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1Pmpz5ftMEn1xpdGVqjkEhEQD5vwvaIyVaQ1fCvCsUXajFOB3UiclxqYy6pdSX5KNe85oJicRH8wCUGnOQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1#id=KeRgN&originHeight=939&originWidth=1028&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

有些读者可能还会有疑问：哈希冲突链上的元素只能通过指针逐一查找再操作。当往哈希表插入数据很多，冲突也会越多，冲突链表就会越长，那查询效率就会降低了。

为了保持高效，Redis 会对**哈希表做rehash**操作，也就是增加哈希桶，减少冲突。为了rehash更高效，Redis还默认使用了两个全局哈希表，一个用于当前使用，称为主哈希表，**一个用于扩容，称为备用哈希表**。

### 4.12 在生成 RDB期间，Redis 可以同时处理写请求么？

**可以的**，Redis提供两个指令生成RDB，分别是**save和bgsave**。

- 如果是save指令，会阻塞，因为是主线程执行的。
- 如果是bgsave指令，是fork一个子进程来写入RDB文件的，快照持久化完全交给子进程来处理，父进程则可以继续处理客户端的请求。

### 4.13 布隆过滤器

应对**缓存穿透**问题，我们可以使用**布隆过滤器**。布隆过滤器是什么呢？

布隆过滤器是一种占用空间很小的数据结构，它由一个很长的二进制向量和一组Hash映射函数组成，它用于检索一个元素是否在一个集合中，空间效率和查询时间都比一般的算法要好的多，缺点是有一定的误识别率和删除困难。

**布隆过滤器原理是？**假设我们有个集合A，A中有n个元素。利用**k个哈希散列**函数，将A中的每个元素**映射**到一个长度为a位的数组B中的不同位置上，这些位置上的二进制数均设置为1。如果待检查的元素，经过这k个哈希散列函数的映射后，发现其k个位置上的二进制数**全部为1**，这个元素很可能属于集合A，反之，**一定不属于集合A**。

来看个简单例子吧，假设集合A有3个元素，分别为{**d1,d2,d3**}。有1个哈希函数，为**Hash1**。现在将A的每个元素映射到长度为16位数组B。

![](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1Pmpz5ftMEn1xpdGVqjkEhEQD5b3kpB93Wrfu8ialTIgTbOwnrO8Ot4gUaklWKvh0HeXs1q3VSgyGhriaQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1#id=P64dA&originHeight=232&originWidth=1075&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

我们现在把d1映射过来，假设Hash1（d1）= 2，我们就把数组B中，下标为2的格子改成1，如下：

![](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1Pmpz5ftMEn1xpdGVqjkEhEQD5VhWH4I6Rib4hFcdeeicODziaH1HKSetd2FFZAFw04eVWYuIG3wmWibVO9Q/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1#id=jZuq4&originHeight=260&originWidth=1056&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

我们现在把**d2**也映射过来，假设Hash1（d2）= 5，我们把数组B中，下标为5的格子也改成1，如下：

![](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1Pmpz5ftMEn1xpdGVqjkEhEQD5m7YmvCUiaibriauUoZVFK66W8nssGwvA1ibA6OYByzn8AURITLLgKvCeBA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1#id=Te9ms&originHeight=254&originWidth=1072&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

接着我们把**d3**也映射过来，假设Hash1（d3）也等于 2，它也是把下标为2的格子标1：

![](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1Pmpz5ftMEn1xpdGVqjkEhEQD5XNOc9ZDm68gmStU7IS7Lq8CNvhicSAnibFfTEJp0YfeaN17kK15CE6sQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1#id=npdQh&originHeight=304&originWidth=1050&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

因此，我们要确认一个元素dn是否在集合A里，我们只要算出Hash1（dn）得到的索引下标，只要是0，那就表示这个元素**不在集合A**，如果索引下标是1呢？那该元素**可能**是A中的某一个元素。因为你看，d1和d3得到的下标值，都可能是1，还可能是其他别的数映射的，布隆过滤器是存在这个**缺点**的：会存在**hash碰撞**导致的假阳性，判断存在误差。

如何**减少这种误差**呢？

- 搞多几个哈希函数映射，降低哈希碰撞的概率
- 同时增加B数组的bit长度，可以增大hash函数生成的数据的范围，也可以降低哈希碰撞的概率

我们又增加一个Hash2**哈希映射**函数，假设Hash2（d1）=6,Hash2（d3）=8,它俩不就不冲突了嘛，如下：

![](https://mmbiz.qpic.cn/mmbiz_png/PoF8jo1Pmpz5ftMEn1xpdGVqjkEhEQD5sUMZcBKY5e8ZdHiaicVYaH2AlzXf9wIkBGX4VsrVKjHrrKx4CTM9kSlw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1#id=crxdF&originHeight=280&originWidth=1004&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

即使存在误差，我们可以发现，布隆过滤器并**没有存放完整的数据**，它只是运用一系列哈希映射函数计算出位置，然后填充二进制向量。如果**数量很大的话**，布隆过滤器通过极少的错误率，换取了存储空间的极大节省，还是挺划算的。

目前布隆过滤器已经有相应实现的开源类库啦，如**Google的Guava类库**，Twitter的 Algebird 类库，信手拈来即可，或者基于Redis自带的Bitmaps自行实现设计也是可以的。

## 5  Redis的常用应用场景

- 缓存
- 排行榜
- 计数器应用
- 共享Session
- 分布式锁
- 社交网络
- 消息队列
- 位操作

### 5.1 缓存

我们一提到redis，自然而然就想到缓存，国内外中大型的网站都离不开缓存。合理的利用缓存，比如缓存热点数据，不仅可以提升网站的访问速度，还可以降低数据库DB的压力。并且，Redis相比于memcached，还提供了丰富的数据结构，并且提供RDB和AOF等持久化机制，强的一批。

### 5.2 排行榜

当今互联网应用，有各种各样的排行榜，如电商网站的月度销量排行榜、社交APP的礼物排行榜、小程序的投票排行榜等等。Redis提供的`zset`数据类型能够实现这些复杂的排行榜。

比如，用户每天上传视频，获得点赞的排行榜可以这样设计：

- 1.用户Jay上传一个视频，获得6个赞，可以酱紫：

```
zadd user:ranking:2021-03-03 Jay 3
```

- 2.过了一段时间，再获得一个赞，可以这样：

```
zincrby user:ranking:2021-03-03 Jay 1
```

- 3.如果某个用户John作弊，需要删除该用户：

```
zrem user:ranking:2021-03-03 John
```

- 4.展示获取赞数最多的3个用户

```
zrevrangebyrank user:ranking:2021-03-03 0 2
```

### 5.3 计数器应用

各大网站、APP应用经常需要计数器的功能，如短视频的播放数、电商网站的浏览数。这些播放数、浏览数一般要求实时的，每一次播放和浏览都要做加1的操作，如果并发量很大对于传统关系型数据的性能是一种挑战。Redis天然支持计数功能而且计数的性能也非常好，可以说是计数器系统的重要选择。

### 5.4 共享Session

如果一个分布式Web服务将用户的Session信息保存在各自服务器，用户刷新一次可能就需要重新登录了，这样显然有问题。实际上，可以使用Redis将用户的Session进行集中管理，每次用户更新或者查询登录信息都直接从Redis中集中获取。

### 5.5 分布式锁

几乎每个互联网公司中都使用了分布式部署，分布式服务下，就会遇到对同一个资源的并发访问的技术难题，如秒杀、下单减库存等场景。

- 用synchronize或者reentrantlock本地锁肯定是不行的。
- 如果是并发量不大话，使用数据库的悲观锁、乐观锁来实现没啥问题。
- 但是在并发量高的场合中，利用数据库锁来控制资源的并发访问，会影响数据库的性能。
- 实际上，可以用Redis的setnx来实现分布式的锁。

### 5.6 社交网络

赞/踩、粉丝、共同好友/喜好、推送、下拉刷新等是社交网站的必备功能，由于社交网站访问量通常比较大，而且传统的关系型数据不太适保存 这种类型的数据，Redis提供的数据结构可以相对比较容易地实现这些功能。

### 5.7 消息队列

消息队列是大型网站必用中间件，如ActiveMQ、RabbitMQ、Kafka等流行的消息队列中间件，主要用于业务解耦、流量削峰及异步处理实时性低的业务。Redis提供了发布/订阅及阻塞队列功能，能实现一个简单的消息队列系统。另外，这个不能和专业的消息中间件相比。

### 5.8 位操作

用于数据量上亿的场景下，例如几亿用户系统的签到，去重登录次数统计，某用户是否在线状态等等。腾讯10亿用户，要几个毫秒内查询到某个用户是否在线，能怎么做？千万别说给每个用户建立一个key，然后挨个记（你可以算一下需要的内存会很恐怖，而且这种类似的需求很多。这里要用到位操作——使用setbit、getbit、bitcount命令。原理是：redis内构建一个足够长的数组，每个数组元素只能是0和1两个值，然后这个数组的下标index用来表示用户id（必须是数字哈），那么很显然，这个几亿长的大数组就能通过下标和元素值（0和1）来构建一个记忆系统。
