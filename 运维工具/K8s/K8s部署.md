## **1、基础环境**
所有机器执行以下：
# 关闭防火墙 
systemctl stop firewalld 
systemctl disable firewalld 
 
# 时间同步 
yum install ntpdate -y 
ntpdate time.windows.com

# 安装epel-release源
yum -y install epel-release


## 2、主机搭建
现在开始master主机上192.168.26.227安装kubernetes Master
2.1、使用yum安装etcd、kubernetes-master
yum -y install etcd kubernetes-master
2.2、编辑：vi  /etc/etcd/etcd.conf文件，修改结果如下：
![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1657014115597-b7dbbc2b-63fe-42f1-8f77-96bb89497dd9.png#averageHue=%2313100f&id=vcAuY&originHeight=724&originWidth=1069&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
2.3、配置：vi  /etc/kubernetes/apiserver文件，配置结果如下：
![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1657014116327-4e6c25e7-302a-4105-82f9-bfef9e3c3ac9.png#averageHue=%23181212&id=j5C38&originHeight=734&originWidth=1582&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
2.4、启动etcd、kube-apiserver、kube-controller-manager、kube-scheduler等服务，并设置开机启动。
for SERVICES in etcd kube-apiserver kube-controller-manager kube-scheduler; do systemctl restart $SERVICES;systemctl enable $SERVICES;systemctl status $SERVICES ; done
2.5、在etcd中定义flannel网络
etcdctl mk /atomic.io/network/config '{"Network":"172.17.0.0/16"}'


## 1 **kuboard-spray部署**

| # 安装docker
curl -fsSL [https://get.docker.com](https://get.docker.com) &#124; bash -s docker --mirror Aliyun 
# 设置开机启动：
chkconfig docker on
# 启动docker
systemctl start docker  


docker run -d \\
  --privileged \\
  --restart=unless-stopped \\
  --name=kuboard-spray \\
  -p 80:80/tcp \\
  -v /var/run/docker.sock:/var/run/docker.sock \\
  -v ~/kuboard-spray-data:/data \\
  eipwork/kuboard-spray:latest-amd64 |
| --- |


## **4、集群安装**
打开部署主机地址，如：[http://8.136.31.107/#/login](http://8.136.31.107/#/login)
账户：admin
密码：Kuboard123


![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1657014116829-8ca3c7a6-c941-46f3-9337-26ef07fc9c52.png#averageHue=%23d0e7d7&id=NBOOD&originHeight=692&originWidth=1903&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1657014117221-f47ef0ee-c14d-479a-9a76-2a0b9f1297c0.png#averageHue=%23e0ecd9&id=l5rJt&originHeight=430&originWidth=1317&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
选择一个k8s环境点击导入，等导入完成后。
![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1657014117571-55868f7d-52c4-47e3-b2d0-41a4db027af8.png#averageHue=%236e756a&id=nLkSi&originHeight=577&originWidth=1485&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
保存成功后，进入节点添加页面：
![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1657014117904-566b74ab-b535-4e07-b0d2-9ae9b8635953.png#averageHue=%23e4e6dd&id=e69jv&originHeight=593&originWidth=1267&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
点击添加节点，第一个节点需要勾选所有（**添加节点不能是主机**）：
![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1657014118242-06b8a9d5-57d7-4dfc-9238-9cccb227f342.png#averageHue=%23f6f8da&id=bo4Wm&originHeight=577&originWidth=1312&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1657014118785-f77157ee-b4c2-4a1e-b2cc-d92a031c69c8.png#averageHue=%23fcc384&id=PEduk&originHeight=782&originWidth=1609&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1657014119363-a4777f93-f0ad-49d8-9ea8-369fd59f9dc0.png#averageHue=%23cce2c3&id=eravd&originHeight=478&originWidth=919&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

如果还有新节点，可以按照以上步骤重新添加即可。

## **5、集群应用**
打开管理主机：[http://8.136.31.140/](http://8.136.31.140/)
账户：admin
密码：Kuboard123
点击集群，管理集群应用：
![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1657014119884-58e86a5a-2e60-45f4-869c-09761df432c3.png#averageHue=%2344cab1&id=BAKbi&originHeight=489&originWidth=709&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1657014120413-a5f75bdc-d8df-4d9e-8558-3532f335cba2.png#averageHue=%237b7460&id=jFAP9&originHeight=660&originWidth=948&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

创建命名空间
![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1657014120952-ee1dc4d0-10d7-469f-908e-07ce01386bbe.png#averageHue=%2378d8bc&id=kw6Sz&originHeight=364&originWidth=1267&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
创建集群应用
![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1657014121451-2ae07dcc-00b5-4794-83a7-9e3535599ba0.png#averageHue=%23f1f6db&id=yNxE4&originHeight=661&originWidth=1241&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## **6、Redis集群部署**
![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1657014121884-d1004d7f-2384-4979-adf2-b396a1b1fa96.png#averageHue=%23f8debb&id=ueKoX&originHeight=637&originWidth=1014&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1657014122463-5858d6bd-cff9-4d9f-a160-0642ec38875d.png#averageHue=%23fcfbfa&id=Yu98r&originHeight=632&originWidth=1584&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1657014122971-eaef6646-ced3-4a8b-8ca8-69c3b5381cd9.png#averageHue=%23eff0c8&id=ntKTr&originHeight=779&originWidth=1594&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
等待几分钟，刷新页面查看安装结果。
![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1657014123484-429e7b82-f912-4a2a-a884-475e7881b775.png#id=DXYO7&originHeight=701&originWidth=1627&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## **7、Rabbitmq集群部署**
![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1657014123977-b8cb3383-3181-446f-89e0-830033e166cf.png#id=TFKWi&originHeight=650&originWidth=1105&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1657014124433-5561b3d3-2905-4488-9e40-b658e742ad26.png#id=KcCNu&originHeight=779&originWidth=1627&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1657014124945-7a62649d-774d-4f4e-a35e-36c26e31a92a.png#id=gAt4u&originHeight=807&originWidth=1727&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1657014125472-d14f761b-1325-4772-9be9-457837db0c35.png#id=PxaXs&originHeight=346&originWidth=841&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
运行以下命令：rabbitmq-plugins enable rabbitmq_management

## **8、mysql集群部署**
![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1657014125731-7a7f30bd-b671-4268-b6c9-530f9fbe0318.png#id=Agt2B&originHeight=651&originWidth=949&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1657014126143-844849ab-f37d-443e-b6a5-321d6644961d.png#id=lxsCj&originHeight=709&originWidth=1613&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1657014126701-5219aff6-f1bd-4804-a779-e6b4f3329730.png#id=gD6dV&originHeight=681&originWidth=1519&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
输入如下命令：

1. 登录MySQL：

mysql -u root -p
ALTER USER 'root'@'localhost' IDENTIFIED BY '123456';

1. 添加远程登录用户：

CREATE USER 'dish' IDENTIFIED WITH mysql_native_password BY 'dish';
GRANT ALL PRIVILEGES ON *.* TO 'dish';


## **9、nacos集群部署**
![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1657014127205-8a55d999-2c79-4ce7-8465-467b76bf2f90.png#id=iECLn&originHeight=630&originWidth=1088&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1657014127744-3f7cc965-3cb1-4962-afce-df8da8dcc9d7.png#id=D2yti&originHeight=740&originWidth=1610&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
部署好，等待集群安装完毕，访问公网地址如：[http://47.111.116.83:8848/nacos/](http://47.111.116.83:8848/nacos/)
账户：nacos
密码：nacos
