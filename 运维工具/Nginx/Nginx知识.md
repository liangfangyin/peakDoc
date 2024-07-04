# 一、Nginx简述

## 1.1、概念

		它是一个开源、轻量级和高性能的 Web 服务器，也用作 HTTP、HTTPS、SMTP、IMAP、POP3 协议的反向代理服务器，另一方面，它也用作 IMAP、POP3 和 IMAP 的 HTTP 负载均衡器、HTTP 缓存和电子邮件代理。，他实现非常高效的反向代理、负载平衡，他可以处理2-3万并发连接数，官方监测能支持5万并发，现在中国使用nginx网站用户有很多，例如：新浪、网易、 腾讯等。

## 1.2、功能

**跨平台、配置简单、方向代理、高并发连接**：处理2-3万并发连接数，官方监测能支持5万并发，内存消耗小：开启10个nginx才占150M内存 ，nginx处理静态文件好，耗费内存少，反向代理将允许我们在端口 80 上托管多个域名，同时使用不同的语言编写后端服务，负载均衡可以把多个提供相同功能的后端服务配置成彼此冗余。

Nginx 负责访问日志、黑名单、负载平衡和提供静态文件访问，后台 Web 服务则可专注于业务逻辑处理，Nginx 是动静分离架构的入口程序。

**内置的健康检查功能**：如果有一个服务器宕机，会做一个健康检查，再发送的请求就不会发送到宕机的服务器了。重新将请求提交到其他的节点上。

使用Nginx的话还能：

1、节省宽带：支持GZIP压缩，可以添加浏览器本地缓存
2、稳定性高：宕机的概率非常小
3、接收用户请求是异步的

**优点：**

1、占内存小，可实现高并发连接，处理响应快
2、可实现http服务器、虚拟主机、方向代理、负载均衡
3、Nginx配置简单/4、可以不暴露正式的服务器IP地址
**缺点：**
动态处理差：nginx处理静态文件好,耗费内存少，但是处理动态页面则很鸡肋，现在一般前端用nginx作为反向代理抗住压力，

## 1.3、运行原理

### 1.3.1、Nginx进程

nginx 在启动后，会以 daemon 的方式在后台运行，后台进程包含一个 master 进程和多个 worker 进程，worker 进程以非 root 用户运行，可以在配置文件中配置运行 worker 进程的用户。

master 进程主要用来管理 worker 进程，包含：接收来自外界的信号，向各 worker 进程发送信号，监控 worker 进程的运行状态，当 worker 进程退出后(异常情况下)，会自动重新启动新的 worker 进程。

worker 进程则是处理基本的网络事件。多个 worker 进程之间是对等的，他们同等竞争来自客户端的请求，各进程互相之间是独立的。一个请求，只可能在一个 worker 进程中处理，一个 worker 进程，不可能处理其它进程的请求。

总结其主要功能分别如下：

主进程(master process)的功能：

1. 读取 Nginx 配置文件并验证其有效性和正确性
2. 按照配置启动、管理和关闭工作进程
3. 接受外界指令，比如重启、升级及关闭服务器等指令
4. 不中断服务，实现平滑升级，重启服务并应用新的配置
5. 开启日志文件

工作进程（woker process）的功能：

1. 接收处理客户端的请求
2. 将请求以此送入各个功能模块进行处理
3. IO 调用，获取响应数据
4. 与后端服务器通信，接收后端服务器的处理结果
5. 缓存数据，访问缓存索引，查询和调用缓存数据
6. 发送请求结果，响应客户端的请求
7. 接收主程序指令，比如重启、升级和退出等

![](https://www.cainiaojc.com/article/image/2022/0213/b5fd8b357a4d68dfd8d731fb1a9b0f51.png#id=WW6mG&originHeight=428&originWidth=688&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

master 进程：负责加载和分析配置文件、管理 worker 进程，平滑升级
worker 进程：接收客户端请求、将请求一次送入各模块过滤、I/O 调用、数据缓存、发送响应
cache相关进程：cache loader (缓存索引重建)与 cache manager( 缓存索引管理)组成
--Cache loader 在 Nginx 服务启动后由主进程生成,根据本地磁盘上缓存建立索引元数据库后退出。
--Cache manager 在元数据更新完成后，对元数据是否过期做出判断。

### 1.3.2、Nginx的模块结构

Nginx 的各种功能和操作都由模块来实现。Nginx 由内核和一系列模块组成，内核提供web服务的基本功能，如启用网络协议，创建运行环境，接收和分配客户端请求，处理模块之间的交互。Nginx 的模块从结构上分为核心模块、基础模块和第三方模块。

核心模块： HTTP 模块、EVENT 模块和 MAIL 模块

基础模块： HTTP Access 模块、HTTP FastCGI 模块、HTTP Proxy 模块和 HTTP Rewrite 模块

第三方模块： HTTP Upstream Request Hash 模块、Notice 模块和 HTTP Access Key 模块及用户自己开发的模块。

这样的设计使 Nginx 方便开发和扩展，Nginx的模块默认编译进 nginx 中，如果需要增加或删除模块，需要重新编译 nginx

## 1.4、应用场景

1、http服务器。Nginx是一个http服务可以独立提供http服务。可以做网页静态服务器。
2、虚拟主机。可以实现在一台服务器虚拟出多个网站，例如个人网站使用的虚拟机。
3、反向代理，负载均衡。当网站的访问量达到一定程度后，单台服务器不能满足用户的请求时，需要用多台服务器集群可以使用nginx做反向代理。并且多台服务器可以平均分担负载，不会应为某台服务器负载高宕机而某台服务器闲置的情况。
4、nginx 中也可以配置安全管理、比如可以使用Nginx搭建API接口网关,对每个接口服务进行拦截。

## 1.5、目录结构

Nginx 文件结构比较简洁，主要包括配置文件和二进制可执行程序，通过安装包形式安装的 nginx 文件结构跟各 Linux 发行版目录规则存放配置文件和二进制文件的位置。

源码安装一般会把所有文件一起放到`/usr/local/nginx`，安装后整体的目录结构及文件功能如下：

```
[root@localhost ~]# tree /usr/local/nginx
/usr/local/nginx
├── client_body_temp                 # POST 大文件暂存目录
├── conf                             # Nginx所有配置文件的目录
│   ├── fastcgi.conf                 # fastcgi相关参数的配置文件
│   ├── fastcgi.conf.default         # fastcgi.conf的原始备份文件
│   ├── fastcgi_params               # fastcgi的参数文件
│   ├── fastcgi_params.default       
│   ├── koi-utf
│   ├── koi-win
│   ├── mime.types                   # 媒体类型
│   ├── mime.types.default
│   ├── nginx.conf                   #这是Nginx默认的主配置文件，日常使用和修改的文件
│   ├── nginx.conf.default
│   ├── scgi_params                  # scgi相关参数文件
│   ├── scgi_params.default  
│   ├── uwsgi_params                 # uwsgi相关参数文件
│   ├── uwsgi_params.default
│   └── win-utf
├── fastcgi_temp                     # fastcgi临时数据目录
├── html                             # Nginx默认站点目录
│   ├── 50x.html                     # 错误页面优雅替代显示文件，例如出现502错误时会调用此页面
│   └── index.html                   # 默认的首页文件
├── logs                             # Nginx日志目录
│   ├── access.log                   # 访问日志文件
│   ├── error.log                    # 错误日志文件
│   └── nginx.pid                    # pid文件，Nginx进程启动后，会把所有进程的ID号写到此文件
├── proxy_temp                       # 临时目录
├── sbin                             # Nginx 可执行文件目录
│   └── nginx                        # Nginx 二进制可执行程序
├── scgi_temp                        # 临时目录
└── uwsgi_temp                       # 临时目录
```

所有结尾为 default 的文件都是备份文件，其他未做注释的目录，为在生产环境中较少用到的目录。

## 1.6、配置解析

### 1.6.1、Nginx配置文件

Nginx 主配置文件 /etc/nginx/nginx.conf 是一个纯文本类型的文件，整个配置文件是以区块的形式组织，通常每一个区块以一对大括号{}来表示开始与结束。
提示：若编译安装则 nginx.conf 位于编译时所指定目录。

- Main 位于 nginx.conf 配置文件的最高层；
- Main 层下可以有 Event、HTTP 层；
- Http 层下面允许有多个 Server 层，用于对不同的网站做不同的配置；
- Server 层下面允许有多个 Location，用于对不同的路径进行不同模块的配置。

![](https://www.cainiaojc.com/article/image/2022/0213/6208fba4a87ef.jpeg#id=A8FwA&originHeight=666&originWidth=530&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

全局配置部分用来配置对整个 server 都有效的参数。主要会设置一些影响 nginx 服务器整体运行的配置指令，主要包括配置运行 Nginx 服务器的用户（组）、允许生成的 worker process 数，进程 PID 存放路径、日志存放路径和类型以 及配置文件的引入等。 示例：

#如下为全局Main配置：

```
user  nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;
```

events 块涉及的指令主要影响 Nginx 服务器与用户的网络连接，常用的设置包括是否开启对多 worker process 下的网络连接进行序列化，是否允许同时接收多个网络连接，选取哪种事件驱动模型来处理连接请求，每个 worker process 可以同时支持的最大连接数等。

#如下为Event配置：

```
events {
     worker_connections  1024;
}
```

http 全局块配置的指令包括文件引入、MIME-TYPE 定义、日志自定义、连接超时时间、单链接请求数上限等。

#如下为http配置：

```
http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    access_log  /var/log/nginx/access.log  main;
    sendfile        on;
    #tcp_nopush     on;
    keepalive_timeout  65;
    #gzip  on;
    include /etc/nginx/conf.d/*.conf;
}
```

提示：通常 Server 配置在独立的/etc/nginx/conf.d/*.conf中，通过引用的方式调用，如下/etc/nginx/conf.d/default.conf：

Server 块也被叫做“虚拟主机”部分，它描述的是一组根据不同 server_name 指令逻辑分割的资源，这些虚拟服务器响应 HTTP 请求，因此都包含在 http 部分。最常见的配置是本虚拟机主机的监听配置和本虚拟主机的名称或 IP 配置。一个 server 块可以配置多个 location 块。

```
server {
    listen       80;
    server_name  localhost;
    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}
```

#### **Nginx全局配置**

```
  user  nginx;					        			#进程用户
  worker_processes  1;				        		#工作进程，配合和CPU个数保持一致
  error_log  /var/log/nginx/error.log warn;	        #错误日志路径及级别
  pid        /var/run/nginx.pid;					#Nginx服务启动的pid
```

#### **Nginx events事件配置**

```
  events {
      worker_connections  1024;						#每个worker进程支持的最大连接数
      use epoll;									#内核模型，select、poll、epoll
  }
```

#### **Nginx公共配置**

```
http {
    include       /etc/nginx/mime.types;			#指定在当前文件中包含另一个文件的指令
    default_type  application/octet-stream;			#指定默认处理的文件类型可以是二进制
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';	#日志格式
    access_log  /var/log/nginx/access.log  main;	#访问日志
    sendfile        on;								#优化静态资源
    #tcp_nopush     on;								#nginx不要缓存数据，而是一段一段发送
    keepalive_timeout  65;							#给客户端分配连接超时时间，服务器会在这个时间过后关闭连接。
    #gzip  on;										#压缩
```

#### **Nginx server配置**

Nginx必须使用虚拟机配置站点，每个虚拟主机使用一个server。

```
server {
        listen       80;			        		#监听端口，默认80
        server_name  localhost;						#提供服务的域名或主机名
        #charset koi8-r;
        #access_log  logs/host.access.log  main;
        location / {								#控制网站访问路径
            root   /usr/share/nginx/html;	        #存放网站的路径
            index  index.html index.htm;	        #默认访问的首页
        }
        #error_page  404              /404.html;	#错误页面
        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;	#定义请求错误，指定错误代码
        location = /50x.html {						#错误代码重定向到新的location
            root   html;
        }
    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {										#server段配置
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;
    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {										#server段配置
    #    listen       443 ssl;
    #    server_name  localhost;
    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;				#SSL证书配置
    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;
    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;
    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}
}
```

提示：index指令中列出多个文件名，Nginx 按指定的顺序搜索文件并返回它找到的第一个文件。

#### **Nginx网站配置**

```
在默认虚拟机 default.conf 基础上新建虚拟机。
[root@nginx ~]# vi /etc/nginx/conf.d/mysite.conf
server {
    server_name  www.cainiaojc.com;
    location / {
        root   /usr/share/nginx/base;
        index  index.html;
    }
}
[root@cainiaojc ~]# mkdir -p /usr/share/nginx/base
[root@cainiaojc ~]# echo '<h1>cainiaojc</h1>' > /usr/share/nginx/base/index.html
[root@cainiaojc ~]# nginx -t -c /etc/nginx/nginx.conf		#检查配置文件
[root@cainiaojc ~]# nginx -s reload							#重载配置文件
```

#### **Nginx配置自定义错误页面**

```
[root@nginx01 ~]# vi /etc/nginx/conf.d/mystie.conf
server {
    server_name  www.cainiaojc.com;
    
    error_page  404 403 500 502 503 504  /error.html;		#配置错误页
    location / {
        root   /usr/share/nginx/base;
        index  index.html;
    }
}

[root@cainiaojc ~]# echo '<h1>Error</h1>' > /usr/share/nginx/error.html
[root@cainiaojc ~]# nginx -t -c /etc/nginx/nginx.conf		#检查配置文件
[root@cainiaojc ~]# nginx -s reload							#重载配置文件
```

#### 禁止访问 htaccess

```
location ~/\.ht {
     deny all;
}
```

#### 禁止访问多个目录

```
 location ~ ^/(picture|move)/ {
      deny all;
      break;
 }
```

#### 禁止访问 /data 开头的文件

```
 location ~ ^/data {
      deny all;
  }
```

#### 禁止访问单个目录

```
 location /imxhy/images/ {
      deny all;
 }
```

#### 允许特定 ip 访问

```
root /usr/share/nginx/rewrite/;
allow 208.97.167.194;
allow 222.33.1.2;
allow 231.152.49.4;
deny all;
```

## 1.7、日志配置

日志使用用来进行数据统计、问题排错的重要手段。本文主要介绍 nginx 日志相关的配置如 access_log、log_format、log_not_found、rewrite_log、error_log 以及 Nginx 日志切割。

**日志相关配置**

nginx日志相关涉及的配置有：
access_log：访问日志；
log_format：日志格式；
rewrite_log：重定向日志；
error_log：错误日志；
nginx 具备非常灵活的日志记录模式，每个级别的配置可以有各自独立的访问日志。日志格式通过 log_format 命令来定义。

### 1.7.1、access_log 配置

语法:

- access_log path [format [buffer=size [flush=time]]];
- access_log path format gzip[=level] [buffer=size] [flush=time];
- access_log syslog:server=address[,parameter=value] [format];
- access_log off; #不记录日志

默认值: access_log logs/access.log combined;
使用默认 combined 格式记录日志：access_log logs/access.log 或 access_log logs/access.log combined;
配置段: http, server, location, if in location, limit_except
参数解释：

- gzip：压缩等级。
- buffer：设置内存缓存区大小。
- flush：保存在缓存区中的最长时间。

### 1.7.2、log_format配置

语法:log_format name string ……;
默认值: log_format combined "……";
配置段: http

释义：name 表示格式名称，string 表示等义的格式。log_format 有一个默认的无需设置的 combined 日志格式，相当于 apache 的 combined 日志格式。

示例1：

```
       log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                         '$status $body_bytes_sent "$http_referer" '
                         '"$http_user_agent"';
```

示例2：

```
       log_format  proxy  '$remote_addr - $remote_user [$time_local] "$request" '
                         '$status $body_bytes_sent "$http_referer" '
                         '"$http_user_agent" "$http_user_agent" ';
```

配置相关变量释义：

![](https://g.yuque.com/gr/latex?remote_addr%EF%BC%9A%E8%A1%A8%E7%A4%BA%E5%AE%A2%E6%88%B7%E7%AB%AF%E5%9C%B0%E5%9D%80%EF%BC%9B%0A#card=math&code=remote_addr%EF%BC%9A%E8%A1%A8%E7%A4%BA%E5%AE%A2%E6%88%B7%E7%AB%AF%E5%9C%B0%E5%9D%80%EF%BC%9B%0A&id=GNw2r)remote_user：表示http客户端请求Nginx认证的用户名；
![](https://g.yuque.com/gr/latex?time_local%EF%BC%9ANginx%E9%80%9A%E7%94%A8%E6%97%A5%E5%BF%97%E6%A0%BC%E5%BC%8F%E4%B8%8B%E7%9A%84%E6%9C%AC%E5%9C%B0%E6%97%B6%E9%97%B4%EF%BC%9B%0A#card=math&code=time_local%EF%BC%9ANginx%E9%80%9A%E7%94%A8%E6%97%A5%E5%BF%97%E6%A0%BC%E5%BC%8F%E4%B8%8B%E7%9A%84%E6%9C%AC%E5%9C%B0%E6%97%B6%E9%97%B4%EF%BC%9B%0A&id=mG8jl)request：request请求行，请求的URL、GET等方法、HTTP协议版本；
![](https://g.yuque.com/gr/latex?request_length%EF%BC%9A%E8%AF%B7%E6%B1%82%E7%9A%84%E9%95%BF%E5%BA%A6%EF%BC%9B%0A#card=math&code=request_length%EF%BC%9A%E8%AF%B7%E6%B1%82%E7%9A%84%E9%95%BF%E5%BA%A6%EF%BC%9B%0A&id=q3i3d)request_time：请求处理时间，单位为秒，精度为毫秒；
![](https://g.yuque.com/gr/latex?status%EF%BC%9Aresponse%E8%BF%94%E5%9B%9E%E7%8A%B6%E6%80%81%E7%A0%81%EF%BC%9B%0A#card=math&code=status%EF%BC%9Aresponse%E8%BF%94%E5%9B%9E%E7%8A%B6%E6%80%81%E7%A0%81%EF%BC%9B%0A&id=guTwN)body_bytes_sent：发送给客户端的字节数，不包括响应头的大小，即服务端响应给客户端body信息大小；
![](https://g.yuque.com/gr/latex?http_referer%EF%BC%9Ahttp%E4%B8%8A%E4%B8%80%E7%BA%A7%E9%A1%B5%E9%9D%A2%EF%BC%8C%E5%8D%B3%E4%BB%8E%E5%93%AA%E4%B8%AA%E9%A1%B5%E9%9D%A2%E9%93%BE%E6%8E%A5%E8%AE%BF%E9%97%AE%E8%BF%87%E6%9D%A5%E7%9A%84%EF%BC%8C%E7%94%A8%E4%BA%8E%E9%98%B2%E7%9B%97%E9%93%BE%E3%80%81%E7%94%A8%E6%88%B7%E8%A1%8C%E4%B8%BA%E5%88%86%E6%9E%90%EF%BC%9B%0A#card=math&code=http_referer%EF%BC%9Ahttp%E4%B8%8A%E4%B8%80%E7%BA%A7%E9%A1%B5%E9%9D%A2%EF%BC%8C%E5%8D%B3%E4%BB%8E%E5%93%AA%E4%B8%AA%E9%A1%B5%E9%9D%A2%E9%93%BE%E6%8E%A5%E8%AE%BF%E9%97%AE%E8%BF%87%E6%9D%A5%E7%9A%84%EF%BC%8C%E7%94%A8%E4%BA%8E%E9%98%B2%E7%9B%97%E9%93%BE%E3%80%81%E7%94%A8%E6%88%B7%E8%A1%8C%E4%B8%BA%E5%88%86%E6%9E%90%EF%BC%9B%0A&id=FFe88)http_user_agent：http头部信息，记录客户端浏览器相关信息；
![](https://g.yuque.com/gr/latex?connection%EF%BC%9A%E8%BF%9E%E6%8E%A5%E7%9A%84%E5%BA%8F%E5%88%97%E5%8F%B7%EF%BC%9B%0A#card=math&code=connection%EF%BC%9A%E8%BF%9E%E6%8E%A5%E7%9A%84%E5%BA%8F%E5%88%97%E5%8F%B7%EF%BC%9B%0A&id=WKjlC)connection_requesta：当前通常一个连接获得的请求数量；
![](https://g.yuque.com/gr/latex?msec%EF%BC%9A%E6%97%A5%E5%BF%97%E5%86%99%E5%85%A5%E6%97%B6%E9%97%B4%EF%BC%8C%E5%8D%95%E4%BD%8D%E4%B8%BA%E7%A7%92%EF%BC%8C%E7%B2%BE%E5%BA%A6%E4%B8%BA%E6%AF%AB%E7%A7%92%EF%BC%9B%0A#card=math&code=msec%EF%BC%9A%E6%97%A5%E5%BF%97%E5%86%99%E5%85%A5%E6%97%B6%E9%97%B4%EF%BC%8C%E5%8D%95%E4%BD%8D%E4%B8%BA%E7%A7%92%EF%BC%8C%E7%B2%BE%E5%BA%A6%E4%B8%BA%E6%AF%AB%E7%A7%92%EF%BC%9B%0A&id=lEXXP)pipe：如果请求是通过HTTP流水线（pipelined）发送，pipe值为‘p’，否则为“.”；
$http_x_forwarded_for：http请求携带的http信息。
提示：如果nginx位于负载均衡器，squid，nginx反向代理之后，web服务器无法直接获取到客户端真实的IP地址了。 $remote_addr获取反向代理的IP地址。反向代理服务器在转发请求的http头信息中，可以增加X-Forwarded-For信息，用来记录客户端IP地址和客户端请求的服务器地址。

### 1.7.3、rewrite_log配置

语法: rewrite_log on | off;

默认值：rewrite_log off;

配置段：http，server，location，if

作用：由ngx_http_rewrite_module模块提供的。用来记录重写日志的，对于调试重写规则建议开启。启用时将在error log中记录notice级别的重写日志。

### 1.7.4、error_log配置

语法：error_log file | stderr | syslog:server=address[,parameter=value] [debug | info | notice | warn | error | crit | alert | emerg];

默认值：error_log logs/error.log error;

配置段：main，http，server，location

作用：配置错误日志。

### 1.7.5、日志切割

第一步重命名日志文件，把 access.log 重命名为按日期的文件。不用担心重命名后 nginx 找不到日志文件而丢失日志。在你未重新打开日志文件前，nginx还是会向你重命名前的文件写日志，linux是靠文件描述符而不是文件名定位文件。

第二步向 nginx 主进程发送 USR1 信号，重新打开日志文件。

nginx主进程接到 USR1 信号后会从配置文件中读取日志文件名称，重新打开该日志文件(以配置文件中的日志名称命名)，并以工作进程的用户作为日志文件的所有者。

重新打开日志文件后，nginx主进程会关闭重名的日志文件 `access_yyyymmdd.log`并通知工作进程使用新打开的日志文件 access.log。

工作进程立刻打开新的日志文件 access.log 开始写入日志，然后关闭重命名的日志文件 access_yyyymmdd.log不再写入日志。

nginx日志按日期自动切割脚本如下

```
#nginx日志切割脚本
#author: http://www.nginx.cn

#!/bin/bash
#日志文件存放目录
logs_path='/usr/local/nginx/logs/'
#pid文件位置
pid_path='/usr/local/nginx/nginx.pid'

#重命名日志文件
mv ${logs_path}access.log ${logs_path}access_$(date -d 'yesterday' +'%Y%m%d').log

#向nginx主进程发信号重新打开日志
kill -USR1 `cat ${pid_path}`
```

保存以上脚本nginx_log.sh。

crontab 设置作业

```
0 0 * * * bash /usr/local/nginx/nginx_log.sh
```

这样就每天的0点0分把nginx日志重命名为日期后缀格式，并重新生成新日志文件。

## 1.8、Nginx 指令和上下文

默认情况下，nginx 配置文件可以位于：

```
/etc/nginx/nginx.conf
/usr/local/etc/nginx/nginx.conf 
/usr/local/nginx/conf/nginx.conf
```

配置文件的位置会根据 Nginx 的安装过程而有所不同。

该文件具有以下内容：

### 1.8.1、Directive

Nginx 中的配置选项称为指令。该选项有名称和参数，必须以分号 (;) 结尾，否则 Nginx 将无法加载配置并产生错误。

**例子：**

```
gzip on;
```

### 1.8.2、Context

当我们在文本编辑器中打开核心 Nginx 配置文件时，首先我们会注意到配置被组织成树状结构，并被花括号包围，即“{”和“}”。这些被大括号包围的位置称为放置配置指令的**上下文**。此外，配置指令及其参数以分号结尾。

这是我们可以声明指令的部分。它类似于编程语言中的作用域。

上下文可以嵌套在其他上下文中，从而创建上下文层次结构。

**例子：**

```
worker_processes 2; # 全局上下文中的指令  
http {              # http 上下文  
    gzip on;        # http 上下文中的指令  
  
  server {          # 服务器上下文  
    listen 80;      # 服务器上下文中的指令  
  }  
}
```

用# 填充的行是注释，Nginx 不会解释。

### 1.8.3、指令类型

由于不同指令的继承模型不同，因此在多个上下文中使用同一个指令时要注意。共有三种类型的指令，每种类型都有其继承模型。

**普通的**

每个上下文有一个值。我们只能在上下文中定义它一次。子上下文可以覆盖父指令，但此覆盖仅在给定的子上下文中有效。

```
gzip on;  
gzip off; # 在同一个上下文中有两个普通指令是非法的   
  
server {  
  location /downloads {  
    gzip off;  
  }  
  
  location /assets {  
    # gzip 在这里有效  
  }  
}
```

**Array**

在同一上下文中添加多条指令会增加值而不是完全覆盖它们。在子上下文中定义指令将覆盖给定子上下文中父级的所有值。

```
error_log /var/log/nginx/error.log;  
error_log /var/log/nginx/error_notive.log notice;  
error_log /var/log/nginx/error_debug.log debug;  
  
server {  
  location /downloads {  
    # 这将覆盖父级所有指令  
    error_log /var/log/nginx/error_downloads.log;  
  }  
}
```

**行动指令Action Directive**

动作是用于改变事物的指令。它们的继承行为将取决于模块。

**例如：**在 rewrite 指令的情况下，每个匹配的指令都会被执行。

```
server {  
  rewrite ^ /foobar;  
  
  location /foobar {  
    rewrite ^ /foo;  
    rewrite ^ /bar;  
  }  
}
```

如果我们尝试访问**/sample**：

- 执行服务器重写，重写从 **/sample** 到 **/foobar**。
- 然后匹配位置 **/foobar**。
- location 里第一个重写被执行，从重写 **/foobar 到 /foo**。
- location 里第二个重写被执行，从重写 **/foo 到 /bar**。

让我们看看**return**指令提供的不同行为：

```
server {  
  location / {  
    return 200;  
    return 404;  
  }  
}
```

从上面的情况来看，200 状态立即返回。

**上下文类型**

让我们看一个例子。

```
# 全局上下文  
 ...  
 ...  
 # http 上下文  
http{  
     ...  
     ...  
     # 服务器上下文  
     server {  
              listen 80;  
              server_name example.com;  
              ...  
              ...  
              # Location 上下文  
              location / {              
                          root /var/www/html;              
                          try_files $uri $uri/ =404;          
                          ...  
                          ...  
             }  
    }  
    # 服务器上下文  
    server {  
             ...  
             ...  
             # Location 上下文  
             location / {   
                         ...  
                         ...  
             }  
    }  
    ...  
    ...  
}
```

从上面的例子中，我们可以看到 HTTP 上下文声明了 HTTP 协议的设置。虚拟主机设置在服务器上下文中声明，包含在 http 上下文中。用于存储 URL 设置的 location 上下文包含在服务器上下文中。

**主要上下文**

最一般的上下文是**主上下文**。它也称为**全局上下文**。主上下文全局设置 Nginx 的设置，并且是唯一未被花括号包围的上下文。

主上下文位于核心 Nginx 配置文件的开头。此上下文的指令不能在任何其他上下文中继承，因此不能被覆盖。

主上下文用于配置在基本级别上影响整个应用程序的详细信息。在主上下文中配置的一些常见详细信息是运行工作进程的用户和组、工作进程总数以及保存主进程 ID 的文件。可以在主上下文级别设置整个应用程序的默认错误文件。

```
user nginx;  
worker_processes auto;  
pid /run/nginx.pid;  
...  
...
```

**事件上下文**

事件上下文为连接处理设置全局选项。事件上下文包含在主上下文中。Nginx 配置中只能定义一个事件上下文。

Nginx 使用基于事件的连接处理模型，因此在此上下文中定义的指令决定了工作进程应如何处理连接。

```
# main context  
events {  
        # events context  
        worker_connections 768;   
        multi_accept on;  
}  
...  
...
```

**HTTP 上下文**

HTTP 上下文用于保存处理 HTTP 或 HTTPS 流量的指令。

HTTP 上下文是事件上下文的兄弟，因此它们必须并排列出，而不是嵌套。他们都是主要上下文的孩子。

较低的上下文处理请求，此级别的指令控制每个虚拟服务器的定义默认值。

```
ser nginx;  
worker_processes auto;  
pid /run/nginx.pid;  
...  
...  
events {  
        # events context  
        worker_connections 768;   
        multi_accept on;  
        ...  
        ...  
}  
http {  
       sendfile on;   
       tcp_nopush on;     
       tcp_nodelay on;    
       keepalive_timeout 65;  
       ...  
       ...  
}
```

**服务器上下文**

服务器上下文在 http 上下文中声明。服务器上下文用于定义 Nginx 虚拟主机设置。HTTP 上下文中可以有多个服务器上下文。服务器上下文中的指令处理对与特定域名或 IP 地址关联的资源的请求的处理。

此上下文中的指令可以覆盖许多可能在 http 上下文中定义的指令，包括文档位置、日志记录、压缩等。 除了从 http 上下文中获取的指令之外，我们还可以配置文件以尝试响应请求、发出重定向和重写，并设置任意变量。

```
user nginx;  
worker_processes auto;  
pid /run/nginx.pid;  
...  
...  
events {  
         # events context  
         worker_connections 768;      
         multi_accept on;  
         ...  
         ...  
 }  
http {  
       sendfile on;   
       tcp_nopush on;     
       tcp_nodelay on;    
       keepalive_timeout 65;  
       ...  
       ...  
  
       server {  
                listen 80;  
                server_name domain1.com;  
                root /var/www/html/wordpress;  
                ...  
       
       }  
       server {  
                listen 80;  
                server_name domain2.com;  
                root /var/www/html/drupal;  
                ...  
       
       }  
}
```

**location上下文**

**location上下文**定义指令来处理客户端的请求。当任何对资源的请求到达 Nginx 时，它会尝试将 URI（统一资源标识符）与其中一个location匹配并相应地处理它。

可以在服务器块内定义多个location上下文。此外，一个location上下文也可以嵌套在另一个location上下文中。

```
http {  
       ...  
       ...  
  
       server {  
                listen 80;  
                server_name domain1.com;  
                root /var/www/html/wordpress;  
                ...  
                location /some_url {    
                # configuration for processing URIs starting with /some_url  
                }  
                location /another_url {    
                # configuration for processing URIs starting with /another_url  
                }      
       }  
       server {  
                listen 80;  
                server_name domain2.com;  
                root /var/www/html/drupal;  
                ...  
                location /some_url {    
                # configuration for processing URIs starting with /some_url    
                }  
                location /some_other_url {    
                # configuration for processing URIs starting with /some_other_url  
                }    
       
       }  
}
```

**upstream上下文**

upstream 上下文用于配置和定义上游服务器。允许此上下文定义后端服务器池，Nginx 可以代理请求时使用的后端服务器。这个上下文通常是我们正在配置的各种类型的代理。

upstream 上下文使 Nginx 能够在代理请求的同时执行负载平衡。此上下文在 HTTP 上下文内部和任何服务器上下文外部定义。

upstream 上下文在服务器或 location 块中按名称引用。然后将某种类型的请求传递给定义好的服务器池。然后 upstream 将使用算法（默认为轮询）来确定需要使用哪个特定服务器来处理请求。

```
http{  
     ...  
     ...  
     upstream backend_servers {  
                                server host1.example.com;  
                                server host2.example.com;  
                                server host3.example.com;  
     }  
  
server {  
             listen 80;  
             server_name example.com;  
             location / {  
                           proxy_pass http://backend_servers;  
             }  
  }  
}
```

**邮件上下文**

尽管 Nginx 最常用作 Web 或反向代理服务器，但它也可以用作高性能邮件代理服务器。用于此类指令的**上下文**称为**邮件上下文**。邮件上下文定义在主上下文或全局上下文内或 http 上下文外。

邮件上下文的主要目的是为在服务器上配置邮件代理解决方案提供一个区域。Nginx 可以将身份验证请求重定向到外部身份验证服务器。然后，它可以提供对 POP3、SMTP 和 IMAP 邮件服务器的访问，以提供实际邮件数据。

通常，邮件上下文如下所示：

```
# main context  
mail {  
       server_name mail.example.com;  
       auth_http   localhost:9000/cgi-bin/nginxauth.cgi;  
       proxy_pass_error_message on;  
       ...  
}  
http {  
  
}  
...  
...
```

**if 上下文**

if 上下文用于允许有条件地执行其中定义的指令。if 上下文就像任何其他编程语言的“if 语句”。如果给定条件返回 true，则 if 上下文将执行包含的指令。

由于某些限制，应尽可能避免使用 if 上下文。

```
http {  
        server {  
                     location /some_url {  
                     if (test_condition) {  
                          # do some stuff here  
                   }  
  
         }  
    }  
}
```

**limit_except 上下文**

limit_except 上下文用于防止在 location 上下文中使用除我们明确允许的方法之外的所有 HTTP 方法。例如，如果某些客户端应该有权访问**POST 内容**并且每个人都应该有能力阅读内容，那么我们可以为此使用**limit_except**上下文。

```
...  
...  
location /wp-admin/ {   
    limit_except GET {   
      allow 127.0.0.1;   
      deny all;   
    }   
}  
...  
...
```

**杂项上下文**

除了上述上下文之外，Nginx 中可用的上下文很少，如下所述。这些上下文依赖于可选模块并且很少使用。

- **split_clients：** split_client 上下文将客户端的请求拆分为两个或多个类别。该上下文定义在 HTTP 上下文中，主要用于 A/B 测试。
- **geo：** geo 上下文对客户端 IP 地址进行分类。它用于根据连接的 IP 地址映射变量的值。
- **charset_map：**此上下文用于将特定字符集添加到“Content-Type”响应头字段。此外，使用上下文，可以将数据从一个字符集转换为另一个字符集，但有一些限制。
- **map：** map上下文用于创建变量，其值依赖于其他变量的值，并在http上下文中定义。
- **perl/perl_set：**用于在 Perl 中实现位置和变量处理程序，并将 Perl 调用插入 SSI。此外，在 perl_set 上下文的帮助下，我们可以为特定变量安装 Perl 处理程序。
- **类型：**类型上下文用正确的文件扩展名映射 MIME 类型。此上下文可能出现在 http 上下文、服务器上下文或位置上下文中。

# 二、高级配置

## 2.1、Nginx 静态网页服务器

### **2.1.1、根目录和索引文件**

root 指令用于定义将用于搜索文件的根目录。为了获得请求文件的路径，NGINX 将请求的 URI 附加到由 root 指令定义的路径。该指令可以放置在服务器 {}、http {} 或位置 {} 上下文中的任何级别。

```
server {  
    root /www/data;  
  
    location / {  
    }  
  
    location /images/ {  
    }  
  
    location ~ \.(mp3|mp4) {  
        root /www/media;  
    }  
}
```

在上面的例子中，NGINX 在文件系统的 /www/data/images/ 目录中搜索以 /images/ 开头的 URI（统一资源标识符）。但是如果 URI 以 .mp3 或 .mp4 扩展名结尾，NGINX 会在 /www/media/ 目录中查找该文件，因为它是在匹配的 locatioin 块中指定的，覆盖了上一级的 root 指令。

如果请求后缀带有斜杠，NGINX 会将其视为对目录的请求，并尝试在该目录中查找索引文件并返回。index 指令指定索引文件的名称（默认值为 index.html）。继续这个例子，如果请求 URI 是_/images/some/path/_，NGINX 传送文件**/www/data/images/some/path/index.html**如果它存在。如果没有，则默认情况下 NGINX 返回 HTTP 代码 404（未找到）。要将 NGINX 配置为返回自动生成的目录列表，请将“on”参数添加到自动索引指令中：

```
location /images/ {  
    autoindex on;  
}
```

我们可以在 index 指令中列出多个文件名。NGINX 按指定顺序查找文件并返回它找到的第一个文件。

```
location / {  
    index index.$geo.html index.htm index.html;  
}
```

为了返回索引文件，NGINX 检查文件是否存在，然后通过将索引文件的名称附加到基本 URI 来进行内部重定向到 URI。这个内部重定向产生一个新的 location 搜索匹配过程，如下例所示：

```
location / {  
    root /data;  
    index index.html index.php;  
}  
  
location ~ \.php {  
    fastcgi_pass localhost:8000;  
    #...  
}
```

在上面的例子中，如果请求中的 URI 是 /path/，并且 /data/path/index.html 不存在但 /data/path/index.php 存在，则内部重定向到 /path/index.php ，然后新的匹配到第二个 location ~ .php { } ,请求被代理。

### 2.1.2、try_files尝试几种选择

try_files 指令可用于检查定义的文件或目录是否存在；如果有，NGINX 会进行内部重定向，如果没有，则返回指定的状态代码。例如，要检查文件是否与请求 URI 对应，请使用 try_files 指令和 $uri 变量，如下所示：

```
server {  
    root /www/data;  
  
    location /images/ {  
        try_files $uri /images/default.gif;  
    }  
}
```

该文件以 URI 的形式定义，使用在当前位置或虚拟服务器的上下文中设置的 root 或 alias 指令进行处理。在这种情况下，如果原始 URI 对应的文件不存在，NGINX 将内部重定向到最后一个参数指定的 URI，返回 /www/data/images/default.gif。

最后一个参数也可以是一个状态码，直接在等号或命名 location 之前。在下面的示例中，如果 try_files 指令的任何参数都没有解析为现有文件或目录，则会返回 404 错误。

```
location / {  
    try_files $uri $uri/ $uri.html =404;  
}
```

### 2.1.3、优化服务内容的性能

加载速度是提供任何内容的关键因素。对我们的 NGINX 配置进行小幅优化可能会提高生产力并有助于达到最佳性能。

**启用发送文件**

默认情况下，NGINX 自己控制文件传输，并在发送之前将文件复制到缓冲区中。当我们启用 sendfile 指令时，它将消除将数据复制到缓冲区的步骤，并允许将数据从一个文件描述符直接复制到另一个文件描述符。或者，为了阻止快速连接完全占用工作进程，我们可以使用 sendfile_max_chunk 指令来限制在单个 sendfile() 调用中传输的数据量（在本例中为 1 MB）：

```
location /mp3 {  
    sendfile           on;  
    sendfile_max_chunk 1m;  
    #...  
}
```

**启用 tcp_nopush**

将 tcp_nopush 指令与指令中的 sendfile 一起添加。这使 NGINX 能够在从 sendfile() 获取数据块后立即在一个数据包中发送 HTTP 响应标头。

```
location /mp3 {  
    sendfile   on;  
    tcp_nopush on;  
    #...  
}
```

## 2.2、虚拟主机

### 2.2.1、虚拟主机类型

Nginx 支持三种类型的虚拟主机配置：

- 基于 IP 的虚拟主机
- 基于域名的虚拟主机
- 基于端口的虚拟主机

### 2.2.2、基于IP虚拟主机

**1. 增加主机IP**

目标主机需要主机配备 2 个以上 ip，配置 ip 不是本文重点，这里不展开。

**2. 创建站点目录和网页**

```
  [root@nginx ~]# mkdir -p /home/wwwroot/ipsite01/
  [root@nginx ~]# mkdir -p /home/wwwroot/ipsite02/
  [root@nginx ~]# echo 'ipsite01' > /home/wwwroot/ipsite01/index.html
  [root@nginx ~]# echo 'ipsite02' > /home/wwwroot/ipsite02/index.html
```

**3. nginx 配置虚拟主机**

```
[root@nginx ~]# vi /usr/local/nginx/conf/ipsite.conf
#添加如下内容
server {
    listen  80;			#监听端口
    server_name  192.168.1.1;	#配置虚拟主机名和IP
    location / {
        root /home/wwwroot/ipsite01/;		#请求匹配路径
        index  index.html;			#指定主页
        access_log  /home/wwwlog/ipsite01.access.log  main;
        error_log   /home/wwwlog/ipsite01.error.log  warn;
    }
}
server {
    listen  80;
    server_name  192.168.1.1;
    location / {
        root /home/wwwroot/ipsite02/;		#请求匹配路径 
        index  index.html;
        access_log  /home/wwwlog/ipsite02.access.log  main;
        error_log   /home/wwwlog/ipsite02.error.log  warn;
    }
}
```

**4. 检查配置文件是否正确并重启加载配置生效**

```
[root@nginx ~]# nginx -t 	                #检查配置文件
[root@nginx ~]# nginx -s reload			#重载配置文件
```

### 2.2.3、基于域名虚拟主机

**1. 创建站点目录和网页**

```
  [root@nginx ~]# mkdir -p /home/wwwroot/domainsite01/
  [root@nginx ~]# mkdir -p /home/wwwroot/domainsite02/
  [root@nginx ~]# echo 'domainsite01' > /home/wwwroot/domainsite01/index.html
  [root@nginx ~]# echo 'domainsite02' > /home/wwwroot/domainsite02/index.html
```

**2. nginx 配置虚拟主机**

```
[root@nginx ~]# vi /usr/local/nginx/conf/domainsite.conf
#添加如下内容
server {
    listen  80;			#监听端口
    server_name  www.cainiaojc.com;	#配置虚拟主机域名
    location / {
        root /home/wwwroot/domainsite01/;		#请求匹配路径
        index  index.html;			#指定主页
        access_log  /home/wwwlog/domainsite01.access.log  main;
        error_log   /home/wwwlog/domainsite01.error.log  warn;
    }
}
server {
    listen  80;
    server_name  man.niaoge.com;
    location / {
        root /home/wwwroot/domainsite02/;		#请求匹配路径 
        index  index.html;
        access_log  /home/wwwlog/domainsite02.access.log  main;
        error_log   /home/wwwlog/domainsite02.error.log  warn;
    }
}
```

**3. 检查配置文件是否正确并重启加载配置生效**

```
[root@nginx ~]# nginx -t 	                #检查配置文件
[root@nginx ~]# nginx -s reload			#重载配置文件
```

### 2.2.5、基于端口虚拟主机

**1. 创建站点目录和网页**

```
  [root@nginx ~]# mkdir -p /home/wwwroot/portsite01/
  [root@nginx ~]# mkdir -p /home/wwwroot/portsite02/
  [root@nginx ~]# echo 'portsite01' > /home/wwwroot/portsite01/index.html
  [root@nginx ~]# echo 'portsite02' > /home/wwwroot/portsite02/index.html
```

**2. nginx 配置虚拟主机**

```
[root@nginx ~]# vi /usr/local/nginx/conf/portsite.conf
#添加如下内容
server {
    listen  8080;			#监听端口
    server_name  www.cainiaojc.com;	#配置虚拟主机域名
    location / {
        root /home/wwwroot/portsite01/;		#请求匹配路径
        index  index.html;			#指定主页
        access_log  /home/wwwlog/portsite01.access.log  main;
        error_log   /home/wwwlog/portsite01.error.log  warn;
    }
}
server {
    listen  8090;
    server_name www.cainiaojc.com;
    location / {
        root /home/wwwroot/portsite02/;		#请求匹配路径 
        index  index.html;
        access_log  /home/wwwlog/portsite02.access.log  main;
        error_log   /home/wwwlog/portsite02.error.log  warn;
    }
}
```

**3. 检查配置文件是否正确并重启加载配置生效**

```
[root@nginx ~]# nginx -t 	                #检查配置文件
[root@nginx ~]# nginx -s reload			#重载配置文件
```

## 2.3、处理请求

我们可以指定多个虚拟服务器，每个服务器由一个**server {}**上下文描述。

```
server {
  listen      *:80 default_server;
  server_name cainiaojc.com;

  return 200 "Hello from javatpoint.co";
}

server {
  listen      *:80;
  server_name nikita.co;

  return 200 "Hello from nikita.co";
}

server {
  listen      *:81;
  server_name deep.co;

  return 200 "Hello from deep.co";
}
```

### 2.3.1、server_name 指令

server_name 指令用于接受多个值；它还用于处理通配符匹配和正则表达式。

```
server_name cainiaojc.com; ＃ 完全符合  
server_name *.cainiaojc.com; # 通配符匹配  
server_name cainiaojc.*; # 通配符匹配  
server_name ~^[ 0 - 9 ]*\.cainiaojc\.com$; # 正则匹配
```

如果有任何歧义，那么 nginx 使用以下顺序：

- 完全匹配名称；
- 以星号开头的最长通配符名称，例如“*examples.org”，
- 以星号结尾的最长通配符名称，例如“mail.*”；
- 第一个匹配的正则表达式。

Nginx 将存储三个哈希表：确切名称、以星号开头的通配符和以星号结尾的通配符。如果结果不在上面声明的表中，则将按顺序测试正则表达式。

```
server_name .cainiaojc.com;
```

它是以下的缩写：

```
server_name  cainiaojc.co  www.cainiaojc.co  *.cainiaojc.co;
```

只有一个区别：.cainiaojc.co 存储在第二个表中，这意味着它比显式声明慢一点。

### 2.3.2、listen 指令

在大多数情况下，我们会看到 listen 指令接受 IP: 端口值。

```
listen 127.0.0.1:80;  
listen 127.0.0.1;    # port :80 is used by default  
  
listen *:81;  
listen 81;           # all ips are used by default  
  
listen [::]:80;      # IPv6 addresses  
listen [::1];        # IPv6 addresses
```

但是，也可以指定 UNIX 域的套接字：

```
listen unix:/var/run/nginx.sock;
```

甚至我们可以使用主机名：

```
listen localhost:80;  
listen netguru.co:80;
```

如果指令不存在，则使用**_:80_。

# 三、应用

## 3.1、反向代理

代理是在内部应用程序和外部客户端之间的服务器，将客户端请求转发到相应的服务器。**Nginx 的反向代理服务器**是代理服务器位于私有网络的防火墙后面，将客户端请求发送到相应的后端服务器。

**负载平衡：**反向代理服务器可以充当驻留在我们后端服务器前面的交通警察，并以提高速度和容量利用率的方式在一组服务器之间分配客户端请求，同时确保没有任何服务器过载。如果服务器未启动，则负载平衡器会将流量重定向到其余的在线服务器。

**Web 加速：** Nginx 反向代理用于压缩出站和入站数据，以及缓存常见请求的内容，这两者都加快了客户端和服务器之间的流量流动。

**安全性和匿名性：**我们可以拦截前往我们后端服务器的客户端的请求，通过这样做，反向代理服务器可以保护他们的身份并作为对安全攻击的额外防御。

当 NGINX 服务器代理请求时，它将请求发送到指定的服务器，获取响应，并将其发送回客户端。可以使用指定的协议向 HTTP 服务器或非 HTTP 服务器发送代理请求。支持的协议包括 FastCGI、uwsgi、SCGI 和 Memcached。

要将请求传递到 HTTP 代理服务器，使用 proxy_pass 指令。例如：

```
location /some/path/ {  
    proxy_pass http://www.example.com/link/;  
}
```

要将请求传递到非 HTTP 代理服务器，请使用适当的 **_pass 指令：

- **fastcgi_pass：**将请求传递给 fastCGI 服务器。
- **uwsgi_pass：**将请求传递给 uwsgi 服务器。
- **scgi_pass：**将请求传递给 SCGI 服务器。
- **memcached_pass：**将请求传递给 memcached 服务器。

## 3.2、负载均衡

Nginx 以高并发、低消耗而闻名，这个特点使其很适合作为一个负载均衡器 (Load Balancer)，有策略地分发请求给不同的后端服务器。避免单点故障之余，亦增强整个系统的可用性，简单说不容易宕机。负载均衡是反代的其中一个用途。本文介绍 Nginx 常用的几个负载均衡策略。

### 3.2.1、热备 (Hot Standby)

在此例中，平时使用 www.example.com 提供服务，bad.example.com 则暂时下线，bak.example.com 作为发生故障时兜底的一台机器。又称故障转移 (failover)。

```
upstream backend {
    server www.example.com;
    server bad.example.com down;
    server bak.example.com backup;
}
```

### 3.2.2、轮询 (round-robin)

介个是经（mò）典（rèn）的负载均衡算法。理论上会跳过不能用的 server，但预设的超时太长，实务上可改成 proxy_connect_timeout 1、proxy_read_timeout 1 和 proxy_send_timeout 1。

按照时间先后来分发请求。在此例中，顾名思义第 1 个 request 走 srv1.example.com，第 2 个走 srv2.example.com，第 3 个走 srv3.example.com，第 4 个走 srv1.example.com……以此类推。

```
upstream backend {
    server srv1.example.com;
    server srv2.example.com;
    server srv3.example.com;
}
```

### 3.2.3、最小连接

因应忙闲不均、负载不均的问题而生。在此例中，Nginx 将试图减轻已经很忙的服务器的压力，把新请求分发至没那么忙的主机。

若对动态负载均衡的算法感兴趣，还有一插件 nginx-upstream-fair 可以实现最快响应法。

```
upstream backend {
    least_conn;
    server srv1.example.com;
    server srv2.example.com;
    server srv3.example.com;
}
```

### 3.2.4、源地址哈希 (ip-hash)

Session 用于跟踪用户操作，涉及身份认证时 (用户系统) 往往能派上用场，而通常情况下会话信息保存在单机上，这就使得有些需求得在同一台服务器上完成，不能换到其他的服务器 (姑且不谈分布式 Session)。

Sticky Sessions (粘滞会话) 将保证一个用户对应一台服务器，从而解决 Session 不一致的问题 (通常表现为无法登录)。出于容灾理由，不建议大量使用。

```
upstream backend {
    ip_hash;
    server srv1.example.com;
    server srv2.example.com;
    server srv3.example.com;
}
```

### 3.2.5、加权负载均衡

集群中难免有算力不均的情况。不同服务器处理速度有快有慢，不问性能的齐头式平等，很可能触发木桶原理的副作用——让配置最差的那个服务器决定整个服务器集群的性能。可跑一次 bench.sh，再依据测试结果给服务器排个序。

在此例中，每 10 个新请求有 6 个被分配至 srv1.example.com，然后各有 2 个被分配至 srv2.example.com 和 srv3.example.com。此例演示的轮询法，事实上最小连接和源地址哈希法也可以分配权重。

```
upstream backend {
    server srv1.example.com weight=3;
    server srv2.example.com;
    server srv3.example.com;
}
```

### 3.2.6、运行状况检查

根据 Nginx 的文档，max_fails 缺省值是 1，fail_timeout 缺省值是 10s。

在此例中，www.example.com 的健康检查会被关闭，一直都标记为可用；www2.example.com 连不上时先重试 2 次，如果还不行就退出服务，下线 1 天，方便运维人员排障。

```
upstream backend {
    server www.example.com max_fails=0;
    server www2.example.com max_fails=2 fail_timeout=1d;
}
```

### 3.2.7、proxy_next_upstream

根据 Nginx 的文档，proxy_next_upstream 含括下列数种情况：

```
error 建立连接 / 发送请求 / 接收响应时出错（缺省值之一）；
timeout 建立连接 / 发送请求 / 接收响应时超时（缺省值之一）；
invalid_header 上游返回空白或无效响应；
http_500 上游返回 500 Internal Server Error；
http_501 上游返回 501 Not Implemented；
http_502 上游返回 502 Bad Gateway；
http_503 上游返回 503 Service Unavailable；
http_504 上游返回 504 Gateway Timeout；
http_404 上游返回 404 Not Found；
http_429 上游返回 429 Too Many Requests；
non_idempotent 解除对非幂等请求 (POST, LOCK, PATCH) 的封印，小心造成重复提交；
off 不得转给下一台服务器。
```

一般来说，即使某一台后端服务器返回了 500，这台服务器也会参与负载均衡，毕竟能收到 HTTP 状态码，就表示它还活着。但这样的结果在用户眼里跟 Connection Refused 以及 Operation Timed Out 可没啥区别，所以在此例中，把 500 一并纳入“**在下一台服务器重试**”的机制里。

```
location / {
    ...
    proxy_pass http://backend;
    proxy_next_upstream error timeout http_500;
    ...
}
```

## 3.3、内容缓存

proxy_cache 运用局部性的原理，备存一些先前被访问过、料将被再度使用的资源，使用户得以由前端服务器直接取得，从而减少后端服务器的资源开销，并缓解整个系统的压力。缓存也是反代的用途之一。本文介绍 Nginx 基本的缓存配置。

要启用缓存，请在顶级 http { } 上下文中添加 proxy_cache_path 指令。重要且强制的第一个参数是缓存内容的本地文件系统路径，keys_zone 参数指定用于存储缓数据的共享内存区域的名称和大小。

**1、proxy_cache_path 定义一个缓存目录**

```
http {
    ...
    proxy_cache_path /usr/local/nginx/cache levels=1:2 keys_zone=the_cache_zone:10m inactive=1h max_size=512m use_temp_path=off;
    ...
}
```

**2、启用缓存**

proxy_cache_bypass 用于指定忽略缓存的情况，当其值为空或为零时，使用缓存。
proxy_cache_key 用于生成缓存键，区分不同的资源。要特别留心 Query String。
proxy_cache_min_uses 则规定缓存门槛，请求过多少次才缓存，不缓存低频请求，避免浪费。

```
location / {
    ...
    proxy_pass http://www.example.com;

    proxy_cache the_cache_zone;
    #           proxy_cache_bypass $is_args;
    # (default) proxy_cache_key $scheme$proxy_host$request_uri;
    #           proxy_cache_min_uses 3;
    
    add_header X-Cache-Status $upstream_cache_status;
    ...
}
```

## 3.4、压缩和解压

压缩过程减少了传输数据的大小。然而，由于压缩发生在运行时，它也可能包括相当大的处理开销，这会对性能产生负面影响。Nginx 在向客户端发送响应之前执行压缩，但不会对已经压缩的响应进行双重压缩。

### 3.4.1、启用压缩

要启用压缩，请添加带有**on**参数的 gzip 指令：

```
gzip on;
```

默认情况下，Nginx 仅使用 text/html（MIME 类型）压缩响应。要使用其他 MIME 类型压缩响应，请添加 gzip_types 指令并列出其他类型。

```
gzip_types text/plain application/xml;
```

我们可以定义要压缩的响应的最小长度，使用 gzip_min_length 指令。默认值为 20 字节。

```
gzip_min_length 1000;
```

### 3.4.2、开启解压

某些客户端不使用 gzip 编码方法处理响应。同时，可能需要动态存储压缩数据或响应并将它们存储在缓存中。为了成功地为接受和不接受压缩数据的客户端提供服务，NGINX 可以在将数据发送到后一种类型的客户端时即时解压缩数据。

要启用运行时解压缩，请使用 gunzip 指令。

```
location /storage/ {  
    gunzip on;  
    ...  
}
```

gunzip 指令可以在与 gzip 指令相同的上下文中定义：

```
server {  
    gzip on;  
    gzip_min_length 1000;  
    gunzip on;  
    ...  
}
```

注意：该指令在一个单独的模块中定义，默认情况下可能不包含在 NGINX 构建中。

### 3.4.3、发送压缩文件

要将压缩文件而不是常规文件发送到客户端，请在适当的上下文中将 gzip_static 指令设置为 on。

```
location / {  
    gzip_static on;  
}
```

在上面的例子中，为了服务一个对 /path/to/file 的请求，NGINX 试图找到并发送文件_/path/to/file.gz_。如果文件不存在，或者客户端不支持 gzip，NGINX 会发送未压缩的文件。

## 3.5、安全控制

### 3.5.1、Nginx SSL 连接

SSL（安全套接字层）连接在将加密数据从客户端计算机发送到网络服务器之前使用证书进行身份验证。

在本节中，我们将介绍如何在 NGINX 上配置 HTTPS 服务器。

要在我们的 nginx.conf 文件中设置 HTTPS 服务器，请将 ssl 参数添加到 server 块中的 listen 指令，然后指定服务器证书和私钥文件的位置：

```
server {  
    listen              443 ssl;  
    server_name         www.cainiaojc.com;  
    ssl_certificate     www.cainiaojc.com.crt;  
    ssl_certificate_key www.cainiaojc.com.key;  
    ssl_protocols       TLSv1 TLSv1.1 TLSv1.2;  
    ssl_ciphers         HIGH:!aNULL:!MD5;  
    #...  
}
```

服务器证书是一个公共实体。它被发送到连接到 Nginx 的每个客户端。

私钥是安全的密钥或实体，应存储在访问受限的文件中。但是 nginx 的 master 进程必须能够读取这个文件。我们还可以将私钥存储在与证书相同的文件中。

```
ssl_certificate     www.cainiaojc.com.cert;  
ssl_certificate_key www.cainiaojc.com.cert;
```

ssl_protocol 和 ssl_ciphers 指令可用于要求客户端在建立连接时使用的 SSL/TLS 版本和加密方案。

### 3.5.2、HTTP 身份验证限制

我们可以通过实施用户名和密码身份验证来限制对我们网站或其某些部分的访问。用户名和密码取自密码文件创建工具（例如 apache2-utils）创建。

**创建密码文件**

要创建用户名-密码对，请使用密码文件创建实用程序，例如 httpd-tools 或 apache2-utils：

\1. 首先，验证是否安装了 httpd-tools 或 apache2-utils。

\2. 创建密码文件和第一个用户，运行带有 -c 标志的 htpasswd 实用程序，用于创建新文件，文件路径名作为第一个参数，用户名作为第二个参数。

```
$ sudo htpasswd -c /etc/apache2/.htpasswd user1
```

按 Enter 并在出现提示时输入 user1 的密码。

\3. 创建额外的用户名-密码对。省略 -c 标志，因为文件已经存在：

```
$ sudo htpasswd /etc/apache2/.htpasswd user2
```

\4. 我们可以确保文件包含配对的用户名和加密密码：

```
$ cat /etc/apache2/.htpasswd  
user1:$apr1$/woC1jnP$KAh0SsVn5qeSMjTtn0E9Q0  
user2:$apr1$QdR8fNLT$vbCEEzDj7LyqCMyNpSoBh/  
user3:$apr1$Mr5A0e.U$0j39Hp5FfxRkneklXaMrr/
```

### 3.5.3、HTTP 基本身份验证

\1. 在我们要保护的 location 内，定义 auth_basic 指令并为受密码保护的区域命名。当要求提供凭据时，该区域的名称将显示在用户名和密码对话框中。

```
location /api {  
    auth_basic "Administrator's Area";  
    #...  
}
```

\2. 使用 auth_basic_user_file 指令指定包含用户/密码对的 .htpasswd 文件：

```
location /api {  
    auth_basic           "Administrator's Area";  
    auth_basic_user_file /etc/apache2/.htpasswd;   
}
```

## 3.6、TCP 和 UDP

### 3.6.1、 Nginx TCP 转发

**以下的配置就是 TCP 转发的最简配置**

- 我们能很明显的发现，stream 模块的配置其实跟 http 模块很类似。但实际上 stream 模块与 http 模块上完全是两套不同的处理流程。用最简单的说法就是，http 模块是基于 Layer7 层的应用层处理流程，而 Stream 仅在 Layer4 层上对连接进行处理。
- 所以 stream 模块无法像 http 模块那样能区分 vhost 主机名，然而这在 stream 模块在引入了 ssl 配置之后又能支持了。而且 stream 模块还能引入 ssl/tls 来对 TCP 连接进行加密。由于 TLS 标准内对 SNI 提供了支持，所以又能识别主机名了。在理论上，stream 模块的端口转发效率实际上相比 http 模块的反向代理效率更高。

```
user  nginx;
worker_processes  1;

events {
    worker_connections  1024;
}

stream {
    # 全局配置
    preread_timeout        120s;
    proxy_connect_timeout  120s;
    proxy_protocol_timeout 120s;
    resolver_timeout       120s;
    proxy_timeout          120s;
    tcp_nodelay            on;

    # 设置日志格式
    log_format proxy '$remote_addr [$time_local] '
                  '$protocol $status $bytes_sent $bytes_received '
                  '$session_time "$upstream_addr" "$upstream_bytes_sent"'
                  '"$upstream_bytes_received" "$upstream_connect_time"';

    access_log /var/log/nginx/stream.access.log proxy;
    error_log  /var/log/nginx/stream.error.log error;

    upstream app_pg {
        hash $remote_addr consistent;
        server 192.168.100.60:5432;
        server 192.168.100.61:5432;
        server 192.168.100.62:5432;
    }

    server {
        # 不指定协议默认是TCP协议
        listen 127.0.0.1:5432 so_keepalive=on;
        proxy_pass app_pg;
    }

    server{
        # keepalive的可配置参数差不多有以下几个：keepidle，keepintvl，keepcnt
        # keepidle为连接保持时间；keepintvl为连接的间隔时间；keepcnt是连接的个数

        # 下示将idle超时设置为30分钟，探测间隔为系统默认值，并将探测计数设置为10个探测器
        # 实际配置的格式为：so_keepalive=on|off|[keepidle]:[keepintvl]:[keepcnt]
        listen *:3306 so_keepalive=30m::10;
        proxy_connect_timeout 10s;
        proxy_timeout 20s;
        proxy_buffer_size 512k;
        proxy_pass 192.168.100.60:8000;
    }
}
```

- **Nginx实现 SSH 转发**

```
user  nginx;
worker_processes  1;

events {
    worker_connections  1024;
}

stream {
    upstream ssh {
            hash $remote_addr consistent;
            server 192.168.1.42:22 weight=5;
       }

    server {
        listen 2222;
        proxy_pass ssh;
       }
}
```

### 3.6.2、 Nginx UDP 转发

**以下的配置就是 UDP 转发的最简配置**

- UDP 转发并不是 stream 模块一开始就支持的，而是在 1.9.3 版本之后的 stream 模块才追加了 UDP 转发支持，所以要配置 UDP 转发前必须得先确定自己的 Nginx 版本是否达到了要求。

```
user  nginx;
worker_processes  1;

events {
    worker_connections  1024;
}

stream {
    # 全局配置
    proxy_timeout 120s;
    tcp_nodelay on;

    # 设置日志格式
    log_format proxy '$remote_addr [$time_local] '
                 '$protocol $status $bytes_sent $bytes_received '
                 '$session_time "$upstream_addr" '
                 '"$upstream_bytes_sent" "$upstream_bytes_received" "$upstream_connect_time"';

    access_log /var/log/nginx/stream.access.log proxy;
    error_log /var/log/nginx/stream.error.log error;

    # 配置dns负载均衡
    upstream dns_upstreams {
        server 1.1.1.1:53 weight=1;
        server 1.0.0.1:53 weight=1;        # weight负载均衡权重
        server 8.8.8.8:53 weight=1 backup; # backup标记为备用服务器
    }

    server{
        listen 53 udp;
        proxy_responses 1; # UDP协议专用；期望后端返回给客户端数据包的数量
        proxy_timeout 20s; # 超时时间
        proxy_pass dns_upstreams;
    }
}
```
