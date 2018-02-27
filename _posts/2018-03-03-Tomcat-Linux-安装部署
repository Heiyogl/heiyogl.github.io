---
layout:     post
title:      Tomcat Linux 安装部署
date:       2018-03-03
author:     Heiyogl
header-img: img/post-bg-home.jpg
catalog: true
tags:
    - JAVA
    - Tomcat
    - LINUX
---

# 准备文件：
apache-tomcat-7.0.61.tar.gz
或执行下载命令：wget http://apache.fayea.com/apache-mirror/tomcat/tomcat-7/v7.0.57/bin/apache-tomcat-7.0.57.tar.gz


# 安装配置
1、假设tomcat压缩包存放在 /home/projects 文件夹，解压压缩包
```
# tar -zxvf apache-tomcat-7.0.61.tar.gz
```

2、启动tomcat
进入bin 目录：
```
$ cd /home/projects/apache-tomcat-7.0.61/bin/
```
执行启动命令：
```
$ ./startup.sh 
```

3、防火墙开放 8080 端口
在 /etc/sysconfig/iptables 文件添加 8080 端口配置：
```
# vi /etc/sysconfig/iptables
-A INPUT -m state --state NEW -m tcp -p tcp --dport 8080 -j ACCEPT
 ```
 
重启防火墙，使端口配置生效： 
```
# service iptables restart
```

# 日常应用
1、测试tomcat
远程机器访问：http://192.168.75.134:8080/  可以看到tomcat 页面表示成功。

2、关闭tomcat
进入bin 目录：
```
$ cd /home/projects/apache-tomcat-7.0.61/bin/
执行关闭命令：
$ ./shutdown.sh 
```

3、查看控制台输出信息
进入tomcat/logs/文件夹下：
```
# tail -f catalina.out 
```

或者直接以 ./catalina.sh run 命令启动tomcat。

