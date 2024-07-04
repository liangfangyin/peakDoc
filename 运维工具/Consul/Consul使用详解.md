Consul介绍：
Consul 是由 HashiCorp 公司推出的开源软件，用于实现分布式系统的服务发现与配置。与其他分布式服务注册与发现的方案，Consul 的方案更“一站式”，内置了服务注册与发现框 架、分布一致性协议实现、健康检查、Key/Value 存储、多数据中心方案，不再需要依赖其他工具（比如 ZooKeeper 等），使用起来也较为简单。


Consul支持平台：
consul是使用go语言开发的服务发现、配置管理中心服务。内置了服务注册与发现框 架、分布一致性协议实现、健康检查、Key/Value存储、多数据中心方案，不再需要依赖其他工具（比如ZooKeeper等）。服务部署简单，只有一个可运行的二进制的包。每个节点都需要运行agent，他有两种运行模式server和client。每个数据中心官方建议需要3或5个server节点以保证数据安全，同时保证server-leader的选举能够正确的进行。


Consul官网下载地址：
[https://www.consul.io/downloads.html](https://www.consul.io/downloads.html)


Consul配置：
windows安装：
我的电脑--->属性---->环境变量，path中添加，如:"D:\consul"

1、单机情况下使用开发模式启动：
consul agent -dev

2、查看版本：
consul version 

3、查看组员：
consul members

4、加入集群：
consul join 192.168.0.101

5、查看更为详细的信息
consul operator raft list-peers

6、Key/Value 写入数据
consul kv put name zhangsan

7、Key/Value 读取数据
consul kv get name

8、Key/Value 获取列表
consul kv get -recurse

9、Key/Value 删除
consul kv delete name

10、Key/Value 删除含某个前缀的所有keys
consul kv delete  -recurse nam






Consul集群配置：
假设有需要搭建三台机器作为集群:
[
  192.168.0.101
  192.168.0.102
  192.168.0.103
]
1、设置Server-Leader 主服务器（老大）
consul agent -server -bootstrap-expect=1 -bind=192.168.40.144 -client=0.0.0.0 -data-dir=D:\tmp\consul -node=consul1 -ui 

2、设置两台从服务器
consul agent -server -bootstrap-expect=1 -bind=192.168.0.160 -client=0.0.0.0 -data-dir=D:\tmp\consul -node=consul2 -ui

consul agent -server -bootstrap-expect=1 -bind=192.168.0.194 -client=0.0.0.0 -data-dir=D:\tmp\consul -node=consul3 -ui

3、将两台从服务器加入到主服务器中作为附属

consul join 192.168.40.160   192.168.0.194





-server?表示是以服务端身份启动
-client=0.0.0.0 表示客户端 IP

-bind?表示绑定到哪个ip（有些服务器会绑定多块网卡，可以通过bind参数强制指定绑定的ip）


-client?指定客户端访问的ip(consul有丰富的api接口，这里的客户端指浏览器或调用方)，0.0.0.0表示不限客户端ip


-bootstrap-expect=3?表示server集群最低节点数为3，低于这个值将工作不正常(注：类似zookeeper一样，通常集群数为奇数，方便选举，consul采用的是raft算法)


-data-dir?表示指定数据的存放目录（该目录必须存在）


-node?表示节点在web ui中显示的名称
-datacenter=dc1 数据中心名称
-advertise	通知展现地址用来改变我们给集群中的其他节点展现的地址，一般情况下-bind地址就是展现地址
-bootstrap	用来控制一个server是否在bootstrap模式，在一个datacenter中只能有一个server处于bootstrap模式，当一个server处于bootstrap模式时，可以自己选举为raft leader
-bootstrap-expect	在一个datacenter中期望提供的server节点数目，当该值提供的时候，consul一直等到达到指定sever数目的时候才会引导整个集群，该标记不能和bootstrap公用
-bind	该地址用来在集群内部的通讯，集群内的所有节点到地址都必须是可达的，默认是0.0.0.0
-client	consul绑定在哪个client地址上，这个地址提供HTTP、DNS、RPC等服务，默认是127.0.0.1
-config-file	明确的指定要加载哪个配置文件
-config-dir	配置文件目录，里面所有以.json结尾的文件都会被加载
-data-dir	提供一个目录用来存放agent的状态，所有的agent允许都需要该目录，该目录必须是稳定的，系统重启后都继续存在
-dc	该标记控制agent允许的datacenter的名称，默认是dc1
-encrypt	指定secret key，使consul在通讯时进行加密，key可以通过consul keygen生成，同一个集群中的节点必须使用相同的key
-join	加入一个已经启动的agent的ip地址，可以多次指定多个agent的地址。如果consul不能加入任何指定的地址中，则agent会启动失败，默认agent启动时不会加入任何节点。
-retry-join	和join类似，但是允许你在第一次失败后进行尝试
-retry-interval	两次join之间的时间间隔，默认是30s
-retry-max	尝试重复join的次数，默认是0，也就是无限次尝试
-log-level	consul agent启动后显示的日志信息级别。默认是info，可选：trace、debug、info、warn、err
-node	节点在集群中的名称，在一个集群中必须是唯一的，默认是该节点的主机名
-protocol	consul使用的协议版本
-rejoin	使consul忽略先前的离开，在再次启动后仍旧尝试加入集群中
-server	定义agent运行在server模式，每个集群至少有一个server，建议每个集群的server不要超过5个
-syslog	开启系统日志功能，只在linux/osx上生效
-ui	使用自带的ui
-ui-dir	提供存放web ui资源的路径，该目录必须是可读的
-pid-file	提供一个路径来存放pid文件，可以使用该文件进行SIGINT/SIGHUP(关闭/更新)agent





