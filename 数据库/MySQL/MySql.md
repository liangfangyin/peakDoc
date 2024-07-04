# 一、安装

## windows

安装包下载地址：[https://downloads.mysql.com/archives/install](https://downloads.mysql.com/archives/installer/)

## docker

**docker命令：**

```
docker pull mysql:latest
```

**运行容器：**

```
docker run -itd --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql
```

--name ：容器名称

-p 3306:3306 ：映射容器服务的 3306 端口到宿主机的 3306 端口，外部主机可以直接通过 宿主机ip:3306 访问到 MySQL 的服务。
MYSQL_ROOT_PASSWORD=123456：设置 MySQL 服务 root 用户的密码

**进入容器：**

```
docker exec -it mysql bash
```

**登录MySQL：**（登录时候输入密码是不可见得，直接输入即可，无需管是否出现）

```
mysql -u root -p
```

**设置密码：**

```
ALTER USER 'root'@'localhost' IDENTIFIED BY '123456';
```

**添加远程登录用户：**

```
CREATE USER 'liangfy' IDENTIFIED WITH mysql_native_password BY '123456';
GRANT ALL PRIVILEGES ON *.* TO 'liangfy';
```

## CenterOS

```
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
wget https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.31-linux-glibc2.12-x86_64.tar.gz

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
```

## 基础设置

### 修改密码

设置Root密码改成"123456"，操作命令如下：

```
use mysql;
update user set authentication_string=password("123456") where user="root";
flush privileges;
```

### 允许Root远程登录

```
use mysql;
grant all privileges on *.* to 'root'@'%' identified by '123456' with grant option;
flush privileges;
```

将root运行远程登录，并且将密码设置为123456.

### 基础参数

**配置文件方式**

Mysql文件目录如 C:/mysql5.7/my.ini

MySQL配置文件my.ini的结构主要包括三个部分：[mysqld]、[mysql]和[client]，其中：

• [mysqld]：用于配置MySQL服务端相关参数，如MySQL的端口号、字符集、日志等。

• [mysql]：用于配置MySQL客户端相关参数，如默认字符集、历史命令记录等。

• [client]：用于配置MySQL命令行客户端相关参数，如默认字符集、历史命令记录等。

[mysqld]

port=3306 #指定MySQL的监听端口，默认为3306

bind-address=0.0.0.0 #指定MySQL所监听的IP地址

character-set-server=utf8mb4 #指定MySQL使用的字符集

max_connections=100 #设置MySQL的最大连接数

log_error=/var/log/mysql/error.log #指定错误日志文件的路径

[client]

default-character-set=utf8mb4 #指定默认字符集

port=3306 #指定MySQL的监听端口，默认为3306

**注意：修改好配置重启mysql服务生效**

**命令方式：**

按照上述参数设置，命令方式一旦mysql服务重启，就会恢复原来得配置。

```
# 设置最大连接数
SET GLOBAL max_connections=1000;
```

### 无法启动

出现这样问题，一般有以下问题：

1、配置错误

2、文件编码变成如UTF-8格式，实际mysql.ini格式是ANSI

# 二、命令

## 数据库：

### 查询数据库

```
show databases;
show databases like 'it%';
```

### 创建数据库

```
create database 数据库名称
如下：
create database if not exists testdb default character set UTF8 default collate utf8_general_ci;
```

### 删除数据库

```
/*方法一*/
drop database 数据库名称;
/*方法二*/
drop database if exists 数据库名称;
如下：
drop database testdb;
```

### 选择数据库

```
use 数据库名称;
如下
use testdb;
```

## 表：

### 创建表

```
create table if not exists 名称
(
  字段名称 数据类型 auto_increment not null default 默认值 comment '字段备注',
  字段名称 数据类型 not null default 默认值 comment '字段备注',
  primary key (主键字段)
);
如下：
create table if not exists user(
  id int not null auto_increment,
  name varchar(50) not null default '' comment '姓名',
  sex int not null default 0 comment '性别',
  age int not null default 0 comment '年龄',
  height int not null default 0 comment '身高',
  create_date datetime not null default now() comment '创建时间',
  primary key (id)
);
```

### 查询表

查询常用语句：

where 条件

group by 分组

having 分组后再聚合

limit  查询条数

order by [asc|desc] 排序，升|降

**常用语句**

```
select 字段1,字段2,字段3.....  from 表名称 where 条件 order by 排序字段1 排序方式,排序字段2 排序方式 limit 条数;
如下：
select name,sex,create_date from user where name like '%张三%' order by id desc limit 100;
```

**分组语句**

```
select 分组字段1,分组字段2,统计  from 表名称  group by 分组字段1,分组字段2;
如下：
不同性别人数：
select sex,count(1) as count from user group by sex;
不同性别平均身高
select sex,avg(height) as count from user group by sex;
不同性别总身高
select sex,sum(height) as count from user group by sex;
```

**查询所有表**

```
show tables;
show tables like '%ba%';
```

**查询指定数据库所有表**

```
show tables from db_name;
show tables from test like 'bl%';
```

**列连接**

```
select name,concat(name,'-',sex) as '姓名+性别' from user;
```

**列别名**

```
select name as uname from user;
```

**运算符**

=：等于

>：大于

<：小于

>=：大于等于

<=：小于等于

<>：不等于

!=：不等于

is null：为null

is not null：不为null

[not]like：模糊查询

[not]between and：在什么范围内

[not]in：在什么范围值内

```
select * from user where age=20;
select * from user where age<>20;
select * from user where age>20;
select * from user where age>=20;
select * from user where age<20;
select * from user where age<=20;
select * from user where age between 18 and 20;
select * from user where age not between 18 and 20;
select * from user where age>=18 and age<=20;
select * from user where age>=18 && age<=20;
select * from user where age in (18,19,28);
select * from user where age not in (18,19,28);
select * from user where name like 'study%';
select * from user where name not like '%005%';
```

**逻辑运算**

非：not

与：and &&

或：or

异或：xor

```
select null is not not null,null is null;
select null<=>null,10<=>null;
select * from user where name='user' and age=20;
select * from user where name='user' or age=21;
```

**去除重复记录**

```
select distinct name from user;
```

**union**

无重并集：把多个结果组合并去重，再以第1列的结果进行升序排序。

```
select name from study11 union select name from study12;
```

**union all**

有重并集：把多个结果组合不去重

```
select name from study11 union all select name from study12;
```

### **插入表**

```
insert into 表名称(字段1,字段2,字段3) values(字段1,字段2,字段3);
insert into 表名称(字段1,字段2,字段3) select 字段1,字段2,字段3 from 其他数据来源;
如下：
insert into user(name,sex,create_date) values('zhangsan',1,'2023-08-02 12:12:12');
insert into user(name,sex,create_date) select name,sex,create_date from user2 where islock=2;
```

### 更新表

```
update 表 set 字段1=值1,字段2=值2  where 条件;
update user set sex=1,name="往往" where id=1;
```

## 函数

### 字符串函数

**1.1、CONCAT(str1,str2,...)**

作用：将传入的字符连接成一个字符串，任何字符与null进行连接结果都是null。

```
SELECT CONCAT(`name`,'-',sex) FROM study11;
```

**1.2、INSERT(str,pos,len,newstr)**

作用：将字符串str从pos位置开始len个字符长的子串，替换为指定的字符newstr。

```
SELECT INSERT('ABCDEFG',2,3,'XXX');
```

说明：SQL Server中对应的函数是STUFF。

```
SELECT STUFF('ABCDEFG',2,3,'XXX')
```

**1.3、LOWER(str)**

作用：将字符串转成小写。

```
SELECT LOWER('ABC');
```

**1.4、UPPER(str)**

作用：将字符串转成大写。

```
SELECT UPPER('abc');
```

**1.5、LEFT(str,len)**

作用：返回字符串str最左边的len个字符。

```
SELECT LEFT('abc',2);
```

**1.6、RIGHT(str,len)**

作用：返回字符串str最右边的len个字符。

```
SELECT RIGHT('abc',2);
```

**1.7、LPAD(str,len,padstr)**

作用：用字符串padstr对str最左边进行填充，直到总长度达到len个字符为止。

```
SELECT LPAD('abc',10,'def');
```

说明：SQL Server中没有对应的函数。

**1.8、RPAD(str,len,padstr)**

作用：用字符串padstr对str最右边进行填充，直到总长度达到len个字符为止。

```
SELECT RPAD('abc',10,'def')；
```

说明：SQL Server中没有对应的函数。

**1.9、LTRIM(str)**

作用：去除字符串当中最左侧的空格。

```
SELECT LTRIM('   abc');
```

**1.10、RTRIM(str)**

作用：去除字符串当中最右侧的空格。

```
SELECT RTRIM('abc   ');
```

**1.11、TRIM([remstr FROM] str)**

完整格式：TRIM([{BOTH | LEADING | TRAILING} [remstr] FROM] str)

作用：返回字符串str，其中所有remstr前缀和/或后缀都已被去除。若分类符BOTH、LEADIN或TRAILING中没有一个是给定的，则假设为BOTH。remstr为可选项，在未指定情况下，可去除空格。

1）去除两侧空格。

```
SELECT TRIM('   abc   ');
```

2）去除两侧'x'字符。

```
SELECT TRIM(BOTH 'x' FROM 'xxxbarxxx');
```

3）去除左侧'x'字符。

```
SELECT TRIM(LEADING 'x' FROM 'xxxbarxxx');
```

4）去除右侧'x'字符。

```
SELECT TRIM(TRAILING 'x' FROM 'xxxbarxxx');
```

5）去除右侧'xyz'字符串。

```
SELECT TRIM(TRAILING 'xyz' FROM 'barxyzxyz');
```

说明：SQL Server中没有对应的函数。

**1.12、REPEAT(str,count)**

作用：返回str重复count次的结果。

```
SELECT REPEAT('abc',3);
```

说明：SQL Server中没有对应的函数。

**1.13、REPLACE(str,from_str,to_str)**

作用：用字符串to_str替换字符串str中所有出现的字符串from_str。

```
SELECT REPLACE('mysql','my','hello my');
```

**1.14、SUBSTRING(str FROM pos FOR len)**

作用：返回字符串str中第pos位置起len个字符长度的字符。

```
SELECT SUBSTRING('abc',2,2);
```

### 数值函数

**2.1、ABS(X)**

作用：返回X的绝对值。

```
SELECT ABS(-1);
```

**2.2、CEILING(X)**

作用：小数不为零部分向上取整，即向上取最近的整数。

```
SELECT CEILING(1.1);
```

**2.3、FLOOR(X)**

作用：小数不为零部分向下取整，即向下取最近的整数。

```
SELECT FLOOR(2.3);
```

**2.4、MOD(N,M)**

作用：返回N/M的模，即求余。

```
SELECT MOD(5,2);
SELECT 5%2;
```

说明：SQL Server中没有对应的函数，只能通过%求余。

```
SELECT 5%2
```

**2.5、RAND()**

作用：返回0-1内容的随机值。

```
SELECT CEILING(RAND()*10);
```

### 日期和时间函数

**3.1、CURDATE()**

作用：返回当前日期，只包含年月日。

```
SELECT CURDATE();
```

说明：SQL Server中没有对应的函数。

**3.2、CURTIME()**

作用：返回当前时间，只包含时分秒。

```
SELECT CURTIME();
```

说明：SQL Server中没有对应的函数。

**3.3、NOW()**

作用：返回当前日期和时间，年月日时分秒都包含。

```
SELECT NOW();
```

说明：SQL Server中对应的函数是GETDATE。

```
SELECT GETDATE()
```

**3.4、UNIX_TIMESTAMP()**

作用：

```
SELECT UNIX_TIMESTAMP();
```

说明：SQL Server中没有对应的函数。

**3.5、FROM_UNIXTIME(unix_timestamp)**

作用：

```
SELECT FROM_UNIXTIME(1599560172);
```

说明：SQL Server中没有对应的函数。

**3.6、WEEK(date)**

作用：返回当前是一年中的第几周。

```
SELECT WEEK(NOW());
```

说明：SQL Server中对应的函数是DATEPART。

```
SELECT DATEPART(WEEK,GETDATE())
```

**3.7、YEAR(date)**

作用：返回所给日期是哪一年。

```
SELECT YEAR(NOW());
```

**3.8、HOUR(time)**

作用：返回当前时间的小时。

```
SELECT HOUR(NOW());
```

说明：SQL Server中对应的函数是DATEPART。

```
SELECT DATEPART(HOUR,GETDATE())
```

**3.9、MINUTE(time)**

作用：返回当前时间的分钟。

```
SELECT MINUTE(NOW());
```

说明：SQL Server中对应的函数是DATEPART。

```
SELECT DATEPART(MINUTE,GETDATE())
```

**3.10、DATE_FORMAT(date,format)**

作用：用于以不同的格式显示日期/时间数据。

![](https://img2020.cnblogs.com/blog/1227623/202009/1227623-20200908200432352-400230058.png#id=gT4Mo&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

```
SELECT DATE_FORMAT(NOW(),'%Y-%m-%d');
SELECT DATE_FORMAT(NOW(),'%Y%m%d');
SELECT DATE_FORMAT(NOW(),'%y%m%d');
```

说明：SQL Server中对应的函数是CONVERT。

```
SELECT CONVERT(VARCHAR(10),GETDATE(),120)
SELECT CONVERT(VARCHAR(10),GETDATE(),112)
SELECT CONVERT(VARCHAR(10),GETDATE(),12)
```

**3.11、DATE_ADD(date,INTERVAL expr unit)**

作用：向日期添加指定的时间间隔。

![](https://img2020.cnblogs.com/blog/1227623/202009/1227623-20200908201550096-2082796035.png#id=prg9c&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

```
SELECT DATE_ADD(NOW(),INTERVAL 3 DAY);
SELECT DATE_ADD(NOW(),INTERVAL 1 MONTH);
```

说明：SQL Server中对应的函数是DATEADD。

```
SELECT DATEADD(DAY,3,GETDATE())
SELECT DATEADD(MONTH,1,GETDATE())
```

**3.12、DATEDIFF(expr1,expr2)**

作用：返回两个日期之间的天数。

```
SELECT DATEDIFF('2020-06-06',NOW());
```

说明：SQL Server中对应的函数是DATEDIFF，不过结果是相反的。

```
SELECT DATEDIFF(DAY,'2020-06-06',GETDATE())
```

### 流程函数

**4.1、IF(expr1,expr2,expr3)**

作用：如果expr1是真，返回expr2，否则返回expr3。

```
SELECT IF(2>3,TRUE,FALSE);
```

说明：SQL Server中对应的函数是IIF。

```
SELECT IIF(2>3,1,0)
```

**4.2、IFNULL(expr1,expr2)**

作用：如果expr1不为空，返回expr1，否则返回expr2。

```
SELECT IFNULL('abc','def');
SELECT IFNULL(NULL,'def');
```

说明：SQL Server中对应的函数是ISNULL。

```
SELECT ISNULL('abc','def')
SELECT ISNULL(NULL,'def')
```

**4.3、CASE WHEN THEN END**

作用：查询满足多种条件的情况。

[![](https://common.cnblogs.com/images/copycode.gif#id=uFkXm&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)](javascript:void(0);)

```
/*写法一*/
#用户变量，需使用@符号，也可以定义为SELECT @sex:='male';
SET @sex='male';
SELECT CASE @sex
    WHEN 'male' THEN
        '男'
    ELSE
        '女'
END AS '性别';

/*写法二*/
SET @score=90;
SELECT 
    CASE 
        WHEN @score BETWEEN 90 AND 100 THEN 'A+'
        WHEN @score BETWEEN 80 AND 89 THEN 'A'
        WHEN @score BETWEEN 60 AND 79 THEN 'B'
        ELSE 'C'
    END AS '评级';
```

[![](https://common.cnblogs.com/images/copycode.gif#id=d0mhX&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)](javascript:void(0);)

### 其它常用函数

**5.1、DATABASE()**

作用：返回当前数据库名。

```
SELECT DATABASE();
```

说明：SQL Server中对应的函数是DB_NAME。

```
--方法一
SELECT DB_NAME()
--方法二
SELECT NAME FROM MASTER..SYSDATABASES WHERE DBID=(SELECT DBID FROM MASTER..SYSPROCESSES WHERE SPID = @@SPID)
```

**5.2、VERSION()**

作用：返回当前数据库版本。

```
SELECT VERSION();
```

**5.3、USER()**

作用：返回当前登录用户名。

```
SELECT USER();
```

说明：SQL Server中对应的函数是SUSER_NAME。

```
SELECT SUSER_NAME()
```

**5.4、PASSWORD(str)**

作用：返回str的PASSWORD加密值。

```
SELECT PASSWORD('abc');
```

说明：SQL Server中没有对应的函数。

**5.5、MD5(str)**

作用：返回str的MD5值。

```
SELECT MD5('abc');
```

说明：SQL Server中对应的函数是HASHBYTES。

```
SELECT HASHBYTES('MD5','abc')
```

## 存储过程

### **创建存储过程**

```
DELIMITER $$
CREATE PROCEDURE 过程名称(参数)
BEGIN
    业务逻辑代码
END$$
DELIMITER ;
```

创建如：

```
DELIMITER $$
CREATE PROCEDURE SHOW_EMP01()
BEGIN
    SELECT * FROM STUDY11;
END$$
DELIMITER ;
```

调用：

```
CALL SHOW_EMP01();
```

### **查询存储过程**

1）查看所有存储过程

```
SHOW PROCEDURE STATUS;
```

2）查看指定数据库的存储过程

```
SHOW PROCEDURE STATUS WHERE DB='test';
```

3）查看指定存储过程源代码

```
SHOW CREATE PROCEDURE SHOW_EMP01;
```

### **删除存储过程**

```
DROP PROCEDURE SHOW_EMP01;
```

### **声明变量**

```
DELIMITER $$
CREATE PROCEDURE SHOW_EMP02()
BEGIN
    #变量定义
    DECLARE ROWS INT DEFAULT 0;    
    #变量赋值
    SELECT COUNT(*) INTO ROWS FROM STUDY11;
    #结果返回
    SELECT ROWS;
END$$
DELIMITER ;
```

### **IN：输入参数**

```
DELIMITER $$
CREATE PROCEDURE GETSEX (IN PNAME VARCHAR(12))
BEGIN
    SELECT SEX FROM STUDY11 WHERE NAME=PNAME;
END$$
DELIMITER ;

调用：
CALL GETSEX ('study01');
```

### **OUT：输出参数**

1）创建：

```
DELIMITER $$
CREATE PROCEDURE GETID (IN PNAME VARCHAR(12),OUT PID INT)
BEGIN
    SELECT ID INTO PID FROM STUDY11 WHERE NAME=PNAME;
END$$
DELIMITER ;
```

2）调用：

```
CALL GETID ('study01',@PID);
SELECT @PID;    -- 此句的完整写法是：SELECT @PID FROM DUAL;
```

### **INOUT：输入输出参数**

1）创建：

```
DELIMITER $$
CREATE PROCEDURE ADDINT (INOUT PNUM INT,IN PINC INT)
BEGIN
    SET PNUM=PNUM+PINC;
END$$
DELIMITER ;
```

2）调用：

```
SET @PNUM=10,@PINC=20;
CALL ADDINT (@PNUM,@PINC);
SELECT @PNUM;
```

## 索引

### 创建索引

语法：CREATE INDEX 索引名称 ON table (column[,column]...);

```
CREATE INDEX ID_INDEX ON emp (ID);
CREATE INDEX NAME_INDEX ON emp (NAME);
```

### 删除索引

语法：DROP INDEX 索引名称 ON 表名;

```
DROP INDEX NAME_INDEX ON emp;
```

### 查看索引

语法：SHOW INDEX FROM 表名;

```
SHOW INDEX FROM emp;
```

### 索引使用

**适合使用索引：**

1）主键（自动建立唯一索引）

2）外键

3）查询中与其它表关联的字段

4）频繁作为查询条件的字段

5）查询中统计或者分组的字段

6）查询中排序的字段

**不适合使用索引：**

1）频繁更新的字段，因为每次更新不单单更新了记录还会更新索引。

2）WHERE条件里用不到的字段

3）表记录太少

4）经常增删改的表

5）如果某个数据列包含太多重复的内容（如性别，年龄。）


## 索引优化
### 独立的列
在进行查询时，索引列不能是表达式的一部分，也不能是函数的参数，否则无法使用索引。
例如下面的查询不能使用 actor_id 列的索引：
```
SELECT actor_id FROM sakila.actor WHERE actor_id + 1 = 5;
```
### 多列索引
在需要使用多个列作为条件进行查询时，使用多列索引比使用多个单列索引性能更好。例如下面的语句中，最好把 actor_id 和 film_id 设置为多列索引。
```
SELECT film_id, actor_ id FROM sakila.film_actor
WHERE actor_id = 1 AND film_id = 1;
```
### 索引列的顺序
让选择性最强的索引列放在前面。
索引的选择性是指：不重复的索引值和记录总数的比值。最大值为 1，此时每个记录都有唯一的索引与其对应。选择性越高，每个记录的区分度越高，查询效率也越高。
例如下面显示的结果中 customer_id 的选择性比 staff_id 更高，因此最好把 customer_id 列放在多列索引的前面。
```
SELECT COUNT(DISTINCT staff_id)/COUNT(*) AS staff_id_selectivity,
COUNT(DISTINCT customer_id)/COUNT(*) AS customer_id_selectivity,
COUNT(*)
FROM payment;
```
```
staff_id_selectivity: 0.0001
customer_id_selectivity: 0.0373
               COUNT(*): 16049
```
### 前缀索引
对于 BLOB、TEXT 和 VARCHAR 类型的列，必须使用前缀索引，只索引开始的部分字符。
前缀长度的选取需要根据索引选择性来确定。
### 覆盖索引
索引包含所有需要查询的字段的值。
具有以下优点：

- 索引通常远小于数据行的大小，只读取索引能大大减少数据访问量。
- 一些存储引擎（例如 MyISAM）在内存中只缓存索引，而数据依赖于操作系统来缓存。因此，只访问索引可以不使用系统调用（通常比较费时）。
- 对于 InnoDB 引擎，若辅助索引能够覆盖查询，则无需访问主索引。
## 索引的优点

- 大大减少了服务器需要扫描的数据行数。
- 帮助服务器避免进行排序和分组，以及避免创建临时表（B+Tree 索引是有序的，可以用于 ORDER BY 和 GROUP BY 操作。临时表主要是在排序和分组过程中创建，不需要排序和分组，也就不需要创建临时表）。
- 将随机 I/O 变为顺序 I/O（B+Tree 索引是有序的，会将相邻的数据都存储在一起）。
## 索引的使用条件

- 对于非常小的表、大部分情况下简单的全表扫描比建立索引更高效；
- 对于中到大型的表，索引就非常有效；
- 但是对于特大型的表，建立和维护索引的代价将会随之增长。这种情况下，需要用到一种技术可以直接区分出需要查询的一组数据，而不是一条记录一条记录地匹配，例如可以使用分区技术。

**为什么对于非常小的表，大部分情况下简单的全表扫描比建立索引更高效？**
如果一个表比较小，那么显然直接遍历表比走索引要快（因为需要回表）。
注：首先，要注意这个答案隐含的条件是查询的数据不是索引的构成部分，否也不需要回表操作。其次，查询条件也不是主键，否则可以直接从聚簇索引中拿到数据。



## 临时表

### 临时表创建

```
CREATE TEMPORARY TABLE `emp_t1` (
  `ID` int(11) DEFAULT NULL,
  `NAME` varchar(50) DEFAULT NULL,
  `AGE` int(11) DEFAULT NULL,
  KEY `ID_INDEX` (`ID`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8
```

### 临时表删除

```
DROP TABLE emp_t1;
```

### 临时表使用（存储过程）

创建：

```
DELIMITER $$
CREATE PROCEDURE TempTest1 ()
BEGIN
    #临时表创建方式一测试
    CREATE TEMPORARY TABLE `emp_t1` (
        `ID` int(11) DEFAULT NULL,
        `NAME` varchar(50) DEFAULT NULL,
        `AGE` int(11) DEFAULT NULL,
        KEY `ID_INDEX` (`ID`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8;
    
    INSERT INTO EMP_T1 SELECT * FROM EMP LIMIT 10;
    
    #临时表创建方式二测试
    CREATE TEMPORARY TABLE emp_t2 SELECT * FROM EMP WHERE ID<=10;
    
    #结果返回
    SELECT * FROM EMP_T1 UNION SELECT * FROM EMP_T2;
    
    #临时表删除
    DROP TABLE EMP_T1,EMP_T2;
END$$
DELIMITER ;
```

调用：

```
CALL TempTest1();
```

## 事务

事务（Transaction）是访问和更新数据库的程序执行单元；事务中可能包含一个或多个sql语句，这些语句要么都执行，要么都不执行。

事务属性：原子性、一致性、隔离性、持久性。

1、原子性：一个事务是一个不可分割的工作单位，事务中包括的诸操作要么都做，要么都不做。

2、一致性：事务必须是使数据库从一个一致性状态变到另一个一致性状态。一致性与原子性是密切相关的。

3、隔离性：一个事务的执行不能被其他事务干扰。即一个事务内部的操作及使用的数据对并发的其他事务是隔离的，并发执行的各个事务之间不能互相干扰。

4、持久性：持久性也称永久性，指一个事务一旦提交，它对数据库中数据的改变就应该是永久性的。接下来的其他操作或故障不应该对其有任何影响。

```
DELIMITER $$
CREATE PROCEDURE TranTest1 ()
BEGIN
    #临时表创建
    CREATE TEMPORARY TABLE EMP_T1 SELECT * FROM EMP WHERE 1=2;
    
    #开启事务
    START TRANSACTION;
    
    #数据插入
    INSERT INTO EMP_T1 VALUES (1,'HELLO',18);
    INSERT INTO EMP_T1 VALUES (2,'WORLD',19);
    
    #提交事务
    COMMIT;
    
    #结果返回
    SELECT * FROM EMP_T1;
    
    #临时表删除
    DROP TABLE EMP_T1;
END$$
DELIMITER ;
```

## 触发器

触发器是与表有关的数据库对象，在满足定义条件时触发，并执行触发器中定义的语句集合。

### 创建触发器

```
CREATE [DEFINER = { 'user' | CURRENT_USER }]　
TRIGGER trigger_name
trigger_time trigger_event
ON table_name
FOR EACH ROW
[trigger_order]
trigger_body
```

![](https://img2020.cnblogs.com/blog/1227623/202009/1227623-20200910194358675-861055093.png#id=Ftc4J&originHeight=345&originWidth=920&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

```
#当插入数据年龄小于18岁，更新18岁
DELIMITER $$
DROP TRIGGER IF EXISTS triEmp1ForInsert$$
CREATE TRIGGER triEmp1ForInsert BEFORE INSERT ON EMP1 FOR EACH ROW
BEGIN
    IF (NEW.AGE<18) THEN
        SET NEW.AGE=18;
    END IF;
END$$
DELIMITER ;
```

```
#EMP1插入新记录而EMP2没有时，将新记录也插入到EMP2中。
DELIMITER $$
DROP TRIGGER IF EXISTS triEmp1ForInsert$$
CREATE TRIGGER triEmp1ForInsert AFTER INSERT ON EMP1 FOR EACH ROW
BEGIN
    IF NOT EXISTS (SELECT 1 FROM EMP2 WHERE ID=NEW.ID) THEN
        INSERT INTO EMP2 (ID,`NAME`,AGE) VALUES (NEW.ID,NEW.NAME,NEW.AGE);
    END IF;
END$$
DELIMITER ;
```

### 查看触发器

```
#查看当前数据库所有触发器
SHOW TRIGGERS;
 
#查看指定数据库所有触发器
SHOW TRIGGERS FROM TEST;

#查看指定数据库指定表所有触发器
SELECT * FROM INFORMATION_SCHEMA.TRIGGERS WHERE EVENT_OBJECT_SCHEMA='TEST' AND EVENT_OBJECT_TABLE='EMP1';
```

### 删除触发器

```
#直接删除触发器
DROP TRIGGER triEmp1ForInsert;

#先检查再删除触发器
DROP TRIGGER IF EXISTS triEmp1ForInsert;
```

## 锁

### 锁表解锁

```
#查询是否锁表
SHOW OPEN TABLES WHERE IN_USE>0;

#查询进程
SHOW PROCESSLIST

#杀死进程(一般到这一步就解锁了)
KILL ID;

#查看正运行的事务
SELECT * FROM INFORMATION_SCHEMA.INNODB_TRX;;

#查看正在锁的事务
SELECT * FROM INFORMATION_SCHEMA.INNODB_LOCKS;

#查看等待锁的事务
SELECT * FROM INFORMATION_SCHEMA.INNODB_LOCK_WAITS;

#解锁表
UNLOCK TABLES;
```

### 加行锁

**创建行锁条件**

1）表中创建索引，SELECT ... WHERE 字段（必须是索引，否则行锁无效）。

注：InnoDB的行锁是针对索引加的锁，不是针对记录加的锁，并且该索引不能失效，否则都会从行锁升级为表锁。

2、必须要有事务，这样才是行锁（排他锁）。

3、在SELECT语句后面加上FOR UPDATE。

```
#加事务
START TRANSACTION;
#加锁
SELECT * FROM EMP WHERE ID<=10 FOR UPDATE;
#解析
EXPLAIN SELECT * FROM EMP WHERE ID<=10 FOR UPDATE;
#睡眠
SELECT SLEEP(3);
#提交
COMMIT;

#加事务
START TRANSACTION;
#加锁
SELECT * FROM EMP WHERE ID BETWEEN 11 AND 20 FOR UPDATE;
#解析
EXPLAIN SELECT * FROM EMP WHERE ID BETWEEN 11 AND 20 FOR UPDATE;
#提交
COMMIT;
```

## 事件

事件（Event）是MySQL在相应的时刻调用的过程式数据库对象。一个事件可调用一次，也可周期性的启动，它由一个特定的线程来管理的，也就是所谓的"事件调度器"。

### **查看事件调度器**

```
#event_scheduler=ON表示开启
SELECT @@event_scheduler;
SHOW VARIABLES LIKE 'event_scheduler';
```

### **开启事件调度器**

```
#方法一
SET GLOBAL event_scheduler=1;
#方法二
SET GLOBAL event_scheduler=ON;
#方法三
在my.cnf中的[mysqld]部分，添加event_scheduler=ON然后重启MySQL。
```

注意：如果想要始终开启事件，那么在使用SET GLOBAL开启事件后，还需要在my.ini（Windows系统）/my.cnf（Linux系统）中添加event_scheduler=ON。因为如果没有添加，MySQL重启后事件又会回到原来的状态。

### 关闭事件调度

```
SET GLOBAL event_scheduler=OFF;
```

### 创建事件

#### 创建语法

```
CREATE
    [DEFINER={user | CURRENT_USER}]
    EVENT [IF NOT EXISTS] event_name
    ON SCHEDULE schedule
    [ON COMPLETION [NOT] PRESERVE]
    [ENABLE | DISABLE | DISABLE ON SLAVE]
    [COMMENT 'comment']
    DO event_body;
```

![](https://img2020.cnblogs.com/blog/1227623/202009/1227623-20200913192638077-1748735863.png#id=mGygt&originHeight=390&originWidth=1024&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

一些常用的时间间隔设置：

<1>每隔5秒钟执行

```
ON SCHEDULE EVERY 5 SECOND
```

<2>每隔1分钟执行

```
ON SCHEDULE EVERY 1 MINUTE
```

<3>每天凌晨1点执行

```
ON SCHEDULE EVERY 1 DAY STARTS DATE_ADD(DATE_ADD(CURDATE(), INTERVAL 1 DAY), INTERVAL 1 HOUR)
```

<4>每个月的第一天凌晨1点执行

```
ON SCHEDULE EVERY 1 MONTH STARTS DATE_ADD(DATE_ADD(DATE_SUB(CURDATE(),INTERVAL DAY(CURDATE())-1 DAY),INTERVAL 1 MONTH),INTERVAL 1 HOUR)
```

<5>每3个月，从现在起一周后开始。

```
ON SCHEDULE EVERY 3 MONTH STARTS CURRENT_TIMESTAMP + 1 WEEK
```

<6>每12个小时，从现在起30分钟后开始，并于现在起四个星期后结束。

```
ON SCHEDULE EVERY 12 HOUR STARTS CURRENT_TIMESTAMP + INTERVAL 30 MINUTE ENDS CURRENT_TIMESTAMP + INTERVAL 4 WEEK
```

**需求描述**：每天22：30将TEST1数据库的EMP2表同步到TEST2数据库的EMP2表。

1）在TEST1数据库上创建存储过程：

```
DELIMITER $$
CREATE PROCEDURE CopyToTestEMP2()
BEGIN
    TRUNCATE TABLE TEST2.EMP2;
    INSERT INTO TEST2.EMP2 SELECT * FROM EMP2;
END$$
DELIMITER ;
```

2）在TEST1数据库上创建事件：

```
SET GLOBAL event_scheduler=ON;

CREATE EVENT IF NOT EXISTS TestEmp2Event
ON SCHEDULE EVERY 1 DAY STARTS DATE_ADD(CURDATE(),INTERVAL 1350 MINUTE)
ON COMPLETION PRESERVE ENABLE
COMMENT '将TEST1数据库的EMP2表同步到TEST2数据库的EMP2表'
DO CALL CopyToTestEMP2();
```

### 查询事件

```
SELECT * FROM information_schema.`EVENTS`;
```

### 删除事件

```
DROP EVENT IF EXISTS TestEmp2Event;
```


## 优化
**1，优化表结构**
（1）尽量使用数字型字段
若只含数值信息的字段尽量不要设计为字符型，这会降低查询和连接的性能，并会增加存储开销。这是因为引擎在处理查询和连接时会逐个比较字符串中每一个字符，而对于数字型而言只需要比较一次就够了。
（2）尽可能的使用 varchar 代替 char
变长字段存储空间小，可以节省存储空间。
（3）当索引列大量重复数据时，可以把索引删除掉
比如有一列是性别，几乎只有男、女、未知，这样的索引是无效的。
**2，优化查询**

- 应尽量避免在 where 子句中使用!=或<>操作符
- 应尽量避免在 where 子句中使用 or 来连接条件
- 任何查询也不要出现select *
- 避免在 where 子句中对字段进行 null 值判断

**3，索引优化**

- 对作为查询条件和 order by的字段建立索引
- 避免建立过多的索引，多使用组合索引
