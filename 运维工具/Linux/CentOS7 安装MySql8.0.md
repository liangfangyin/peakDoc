===========================第一步：安装MySql5.7================================

1、检查系统是否安装过mysql
1.1、检查系统中有无安装过mysql
rpm -qa|grep mysql

1.2、查询所有mysql 对应的文件夹，全部删除
whereis mysql
find / -name mysql

1.3、如存在安装的mysql，进行卸载，否则忽略
1.3.1、查看mysql安装
rpm -qa|grep -i mysql

1.3.2、卸载前关闭mysql服务
rpm -ev --nodeps 服务名称


2、卸载CentOS7系统自带mariadb
2.1、查看系统自带的Mariadb
rpm -qa|grep mariadb

2.2、卸载系统自带的Mariadb
rpm -e --nodeps  mariadb服务名称

2.3、删除etc目录下的my.cnf ，一定要删掉，等下再重新建，之前我将就用这个文件，后面改配置各种不生效
rm /etc/my.cnf

3、检查有无安装过mysql 用户组
3.1、检查mysql 用户组是否存在
cat /etc/group | grep mysql
cat /etc/passwd |grep mysql

3.2、如果存在，进行删除，否则忽略
userdel  mysql
groupdel  mysql

3.3、创建mysql 用户组和用户
groupadd mysql
useradd -r -g mysql mysql

4、切换到/usr/local/目录下
cd /usr/local/

5、下载安装
wget [https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.31-linux-glibc2.12-x86_64.tar.gz](https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.31-linux-glibc2.12-x86_64.tar.gz)

6、解压安装mysql
tar -zxvf mysql-5.7.31-linux-glibc2.12-x86_64.tar.gz

7、更名为mysql
mv mysql-5.7.31-linux-glibc2.12-x86_64 mysql

8、更改mysql 目录下所有文件夹所属的用户组和用户，以及权限
chown -R mysql:mysql /usr/local/mysql
chmod -R 777  /usr/local/mysql

9、进入/usr/local/mysql/bin/目录，编译安装初始化mysql，将会生成临时密码，请复制出来
./mysqld --initialize --user=mysql --datadir=/usr/local/mysql/data --basedir=/usr/local/mysq

10、编写配置文件my.cnf，
vi /etc/my.cnf
#############################内容如下start############################

[mysqld]
datadir=/usr/local/mysql/data
port = 3306
sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES
symbolic-links=0
max_connections=400
innodb_file_per_table=1
#表名大小写不明感，敏感为
lower_case_table_names=1

#############################内容如上end############################

编写完，请务必到/etc/my.cnf查看文件是否正确，如果不正确，执行
10.1、设置my.cnf文件编写权限
sudo chmod -R 777 /etc/my.cnf  

10.2、将上述配置文件复制其中，并且保存


11、启动mysql 服务器
11.1、查询服务
ps -ef|grep mysql
ps -ef|grep mysqld

11.2、结束上述进程
kill -9  进程PID

11.3、启动服务
 /usr/local/mysql/support-files/mysql.server start

12、添加软连接，并重启mysql 服务
12.1、添加软连接
ln -s /usr/local/mysql/support-files/mysql.server /etc/init.d/mysql
ln -s /usr/local/mysql/bin/mysql /usr/bin/mysql

12.2、重启mysql服务
service mysql restart


13、登录mysql ，密码就是初始化时生成的临时密码，如果输入一直不正确，直接复制粘贴进去
mysql -u root -p

14、修改密码，因为生成的初始化密码难记
set password for root@localhost = password('新密码');

15、开放远程连接
use mysql;
update user set user.Host='%' where user.User='root';
flush privileges;

16、设置开机自启
16.1、将服务文件拷贝到init.d下，并重命名为mysql
cp /usr/local/mysql/support-files/mysql.server /etc/init.d/mysqld

16.2、赋予可执行权限
chmod +x /etc/init.d/mysqld

16.3、添加服务
chkconfig --add mysqld

16.4、显示服务列表
chkconfig --list



===========================第二步：设置防火墙================================
1、查看防火墙状态
systemctl status firewalld

2、启动防火墙
systemctl start firewalld

3、开放指定端口
firewall-cmd --zone=public --add-port=3306/tcp --permanent

4、重启防火墙
firewall-cmd --reload

