==============第一步：Net Core环境安装====================
1、注册 Microsoft 密钥和源
sudo rpm -Uvh [https://packages.microsoft.com/config/centos/7/packages-microsoft-prod.rpm](https://packages.microsoft.com/config/centos/7/packages-microsoft-prod.rpm)

2、安装 .NET Core SDK
sudo dnf install dotnet-sdk-5.0

3、安装 .NET Core 运行时
sudo yum install dotnet-runtime-5.0


================第二步：手动调试站点是否运行正常===================
1、新建文件夹/usr/website/web/
mkdir  /usr/website/web/

2、设置文件夹权限
sudo chmod -R 777 /usr/website/web/

3、进入/usr/website/web/文件夹
cd  /usr/website/web/

4、启动站点
默认端口：dotnet  ./Web.dll 
指定端口：dotnet ./Web.dll --urls http://*:9002
注意：appsetting.json文件EndpointDefaults下"Protocols": "Http2"删除



===============第三步：设置开机启动站点=================
1、创建服务
vi  /etc/systemd/system/dishweb.service
输入以下内容：
#################################################################################
#!/bin/bash
[Unit]
Description="dish Service"

[Service]
Type=simple
GuessMainPID=true
WorkingDirectory=/usr/website/web/
StandardOutput=journal
StandardError=journal
ExecStart=/usr/bin/dotnet Web.dll
Restart=always
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=root
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false

[Install]
WantedBy=multi-user.target
##########################################################################################
按下ESC按键，输入"  :wq "保存并退出

2、启动 dishweb.service 服务
systemctl start  dishweb

3、添加到开机启动（Failed to execute operation: File exists报错可以用：systemctl disable php-fpm.service 命令清除掉）
systemctl enable dishweb.service

4、查看服务状态
systemctl status dishweb.service

5、刷新服务状态
sudo systemctl daemon-reload



===========================第四步：设置防火墙================================
1、查看防火墙状态
systemctl status firewalld

2、启动防火墙
systemctl start firewalld

3、开放指定端口
firewall-cmd --zone=public --add-port=80/tcp --permanent
firewall-cmd --zone=public --add-port=5001/tcp --permanent

4、重启防火墙
firewall-cmd --reload


=====================第五步：设置Nginx（根据实际情况）=================
1、添加yum源
sudo rpm -Uvh [http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm](http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm)

2、安装
sudo yum install -y nginx

3、启动
sudo systemctl start nginx.service

4、设置开机启动
sudo systemctl enable nginx.service

5、找到etc/nginx目录打开nginx.conf文件修改配置

6、重启nginx
nginx -s reload



























































