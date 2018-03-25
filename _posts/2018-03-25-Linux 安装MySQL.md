
---
layout:     post
title:      Linux 安装MySQL
subtitle:   Linux,MySQL
date:       2018-03-25
author:     Heiyogl
header-img: img/post-bg-home.jpg
catalog: true
tags:
    - JAVA
    - MySQL
    - LINUX
---


# 1、安装 MySQL
（单独安装，与业务系统的数据库分开）  
查看该操作系统上是否已经安装了 mysql 数据库
```
# rpm -qa | grep mysql 
```

有的话，可以通过rpm -ev mysql-libs-5.1.73-3.el6_5.x86_64 --nodeps 命令来卸载掉

```
# yum install mysql-server mysql mysql-devel
 
# service mysqld start
 
# chkconfig --list | grep mysqld
 
mysqld 0:off 1:off 2:off 3:off 4:off 5:off 6:off
```

用上面的命令查看到 MySQL 并没有设置开机启动，所以需要设置开机启动
```
# chkconfig mysqld on
```

为了方便远程管理，防火墙中打开 3306 端口
```
# vi /etc/sysconfig/iptables
```

```
-A INPUT -m state --state NEW -m tcp -p tcp --dport 3306 -j ACCEPT
```

重启防火墙，使端口配置生效
```
# service iptables restart
```
设置 MySQL 数据库 root 用户的密码：
```
# mysqladmin -u root password 'root'
```
登录数据库：
```
# mysql -u root -p
``` 
 
MySQL 授权远程访问（先用 root 登录 mysql）
```
mysql>grant all on *.* to root@'%' identified by 'root';
 
mysql> FLUSH PRIVILEGES;
```

# 2、配置 MySQL
数据库最好使用 InnoDB 引擎，可提高性能。
看你的 mysql 现在已提供什么存储引擎：
```
mysql> show engines;
```
![image](https://note.youdao.com/yws/public/resource/5ffa62ff7b192e63fcab32dbe759c0fa/xmlnote/67F2CEB614CD4482B30E7C39CD65ED77/22487)


看你的 mysql 当前默认的存储引擎:
```
mysql> show variables like '%storage_engine%';
```
![image](https://note.youdao.com/yws/public/resource/5ffa62ff7b192e63fcab32dbe759c0fa/xmlnote/8E6C1ACFE0E7404B8789BBC301CAE1B5/22494)

 
 修改 MySQL 存储引擎为 InnoDB, 在配置文件/etc/my.cnf 中的
 
[mysqld] 下面加入 default-storage-engine=INNODB
```
 # vi /etc/my.cnf [mysqld]
 default-storage-engine=INNODB
``` 

重启 mysql 服务器
```
 # service mysqld restart
``` 
 
再次登录 MySQL 查看默认引擎设置是否生效 
```
mysql> show variables like '%storage_engine%';
```

innodb_buffer_pool_size 参数值设置得尽可能大一点
 
这个参数主要作用是缓存 innodb 表的索引，数据，插入数据时的缓冲默认值：128M，专用 mysql 服务器设置的大小：操作系统内存的 70%-80%最佳。
 
设置方法：my.cnf 文件[mysqld] 下面加入 innodb_buffer_pool_size 参数
```
# vi /etc/my.cnf [mysqld]
 
innodb_buffer_pool_size = 256M
``` 

（这里设置为 256M，因为不是专用的 MySQL 数据库服务器，还有很多其他的服务需要占用系统内存）

设置 MySQL 的查询缓存 query_cache_size ,最少设置 15M
```
# vi /etc/my.cnf [mysqld]
 
query_cache_type=1
 
query_cache_size=32M
``` 
 
重启 mysql 服务器
```
# service mysqld restart
```
 
验证缓存设置是否生效： 
```
mysql> show variables like '%query_cache%';
```


存储空间管理/切换（待续）

