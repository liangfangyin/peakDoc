# 一、安装docker
**1、下载docker**
curl -fsSL [https://get.docker.com](https://get.docker.com) | bash -s docker --mirror Aliyun 
**2、设置开机启动**
systemctl enable docker.service
**3、启动docker**
systemctl start docker
**4、查看docker 启动状态**
systemctl status docker

# **二、安装rancher**
**1、下载rancher**
docker pull rancher/rancher
**2. 创建Rancher挂载目录**
mkdir -p ../software/rancher/rancher_home/rancher
mkdir -p ../software/rancher/rancher_home/auditlog
**3、部署Rancher**
docker run -d --privileged --restart=unless-stopped -p 8080:80 -p 443:443 -v /software/rancher/rancher_home/rancher:/var/lib/rancher -v /software/rancher/rancher_home/auditlog:/var/log/auditlog  --name rancher rancher/rancher 
**4、设置防火墙**
开启防火墙
systemctl start firewalld
开放指定端口
firewall-cmd --zone=public --add-port=80/tcp --permanent
firewall-cmd --zone=public --add-port=8080/tcp --permanent
firewall-cmd --zone=public --add-port=443/tcp --permanent
重启防火墙
firewall-cmd --reload
**5、在外面测试是否访问**（阿里云安全组请实现设置好以上端口可以访问 80/8080/443,上述运行正常需要等待1`2分钟，才能正常访问UI）
[http://IP:8080/](http://47.110.161.145:8080/)

1. **设置管理员登录密码**

获取正在运营的docker镜像，找到rancher：
docker ps 
利用以下命令生成，随机密码：
docker logs   镜像ID   2>&1 | grep "Bootstrap Password:"
将生成的随机密码输入到以下输入框中，进行登录。
![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1657014001754-bb52e45a-8093-457a-a550-2a4c2c6532f8.png#)

1. **设置访问地址**

![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1657014002263-224aea5b-0181-4e73-9f0e-3d3a14395624.png#)
自定义新密码（登录用户：admin，可设置密码：sovell202206），并设置服务的请求地址，然后点击继续。
![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1657014002805-bc6fdd38-be6d-40b5-8fd2-212f20e7219d.png#)


# **三、rancher安装集群**
**1、创建集群（注：主节点和从节点不能在主机上面创建，会导致无法集群）**
![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1657014003172-2b1e1889-fbf2-4d41-a489-e175c16a560d.png#)
点击”创建”按钮
![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1657014003456-ab7a128a-70e3-4643-936a-c76bb28eea07.png#)
点击”自定义”集群
![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1657014003886-dda889d1-6457-4661-a065-530da1d4c8b2.png#)
![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1657014004251-97686f65-3fea-4834-adb0-44208ca8a004.png#)
复制以上命令到，主机运行。
![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1657014004588-2568635e-d4df-4f9a-96d2-f027c3bfe06f.png#)
返回集群列表，选择刚刚创建的集群。
![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1657014005022-21843a78-d389-43b2-8001-485957e734dc.png#)
这个过程需要等待大概5分钟左右才能正常。
![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1657014005530-e98de495-b6b9-4e12-9b4e-27b58e27de27.png#)
查看集群列表，当显示active，代表集群安装成功。

**2、异常处理**
**注意：**Rancher搭建集群：[etcd] Failed to bring up Etcd Plane: etcd cluster is unhealthy: hosts，运行以下命令：
1、docker stop $(docker ps -aq)
2、docker system prune -f
3、docker volume rm $(docker volume ls -q)
4、docker image rm $(docker image ls -q) 
5、rm -rf /etc/ceph  /etc/cni  /etc/kubernetes /opt/cni /opt/rke  /run/secrets/kubernetes.io /run/calico /run/flannel /var/lib/calico  /var/lib/etcd  /var/lib/cni   /var/lib/kubelet /var/lib/rancher/rke/log  /var/log/containers        /var/log/pods 



















