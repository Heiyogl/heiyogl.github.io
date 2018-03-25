---
layout:     post
title:      SonarQube代码质量管理平台的安装
subtitle:   Sonar,CI
date:       2018-03-25
author:     Heiyogl
header-img: img/post-bg-home.jpg
catalog: true
tags:
    - 持续集成
    - CI
    - Sonar
---


# 硬件要求
1GB 内存以上
 
# 环境
CentOS 6.6、JDK7、MySQL5.1 、SonarQube-4.5.4(LTS)

root 用户操作：
准备工作：安装 JDK7 并配置好了环境变量
 
# 安装 MySQL5.1
 具体操作查看站内《Linux 安装MySQL》文章介绍。
 

# 配置 MySQL
 
结合 SonarQube，MySQL 数据库最好使用 InnoDB 引擎，可提高性能。
 
看你的 mysql 现在已提供什么存储引擎：
```
mysql> show engines;
```

 
 
看你的 mysql 当前默认的存储引擎:
```
mysql> show variables like '%storage_engine%';
```
 
 
 
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
``` 
innodb_buffer_pool_size = 256M

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

 
创建 sonarqube 数据库（UTF-8 编码）
 
 
 
 
# 安装 SonarQube 的 Web Server
 
下载最新 LTS 版的 SonarQube 安装包（当前版本为 sonarqube-4.5.4.zip）：下载地址：http://www.sonarqube.org/downloads/

![image](https://camo.githubusercontent.com/d1f65167e7ecbd4452d5c6f160a8cd33d1d5254b/68747470733a2f2f6e6f74652e796f7564616f2e636f6d2f7977732f7075626c69632f7265736f757263652f38646632346134336339363634323763656564323639326236363265363566382f786d6c6e6f74652f37423541383438343536453334354638394141333832413537413831454637382f3232353436)
 
http://dist.sonar.codehaus.org/sonarqube-4.5.4.zip


下载：
```
# wget http://dist.sonar.codehaus.org/sonarqube-4.5.4.zip
``` 
解压安装： 
```
# unzip sonarqube-4.5.4.zip
# mv sonarqube-4.5.4 sonarqube
``` 
编辑 sonar 配置： 
```
# cd sonarqube/conf/ 
# vi sonar.properties 
sonar.jdbc.username=root
sonar.jdbc.password=root

#----- MySQL 5.x 
sonar.jdbc.url=jdbc:mysql://localhost:3306/sonarqube?useUnicode=true&characterE ncoding=utf8&rewriteBatchedStatements=true&useConfigs=maxPerformance
 
#--------------WEB SERVER
sonar.web.host=0.0.0.0
 
sonar.web.context=/sonarqube
 
sonar.web.port=9090
``` 
保存以上配置（注意，要看看默认的 9000 端口是否已被占用）
 
防火墙中打开 9090 端口：
```
# vi /etc/sysconfig/iptables
-A INPUT -m state --state NEW -m tcp -p tcp --dport 9090 -j ACCEPT
```

重启防火墙，使端口配置生效 
```
# service iptables restart
``` 
启动 SonarQube Web Server
```
$ /home/heiyogl/projects/sonarqube-4.5.4/bin/linux-x86-64/sonar.sh start
``` 
（初次启动会自动建表和做相应的初始化）
 
浏览器中输入：http://192.168.2.1:9090/sonarqube/
(第一次访问非常慢，会出现访问不了的现象，等几分钟)  
![image](https://camo.githubusercontent.com/0d049d9043119a9af8ee7645c8c03199bc14ddf3/68747470733a2f2f6e6f74652e796f7564616f2e636f6d2f7977732f7075626c69632f7265736f757263652f38646632346134336339363634323763656564323639326236363265363566382f786d6c6e6f74652f31364234363537444343443534374435384435303830314545333633383346462f3232353530)
  
 
登录，默认用户名/密码为 admin/admin

![image](https://camo.githubusercontent.com/8b6c08cda9db60d02dc5f7356930ebf019064549/68747470733a2f2f6e6f74652e796f7564616f2e636f6d2f7977732f7075626c69632f7265736f757263652f38646632346134336339363634323763656564323639326236363265363566382f786d6c6e6f74652f34353642333235423634454534323644394635454342414431313039353241412f3232353532)

到此，SonarQube 已安装完毕，对 SonarQube 的配置和使用待续。
