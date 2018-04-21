---
layout:     post
title:      MySQL root忘记密码的解决办法
subtitle:   MySQL 安全模式修改密码 
date:       2018-04-21
author:     Heiyogl
header-img: img/post-bg-home.jpg
catalog: true
tags:
    - MySQL
---

**MySQL root忘记密码的解决办法**

# 1、停止MYSQL服务
CMD打开DOS窗口，输入 net stop mysql
```
net stop mysql
```



# 2、进入MYSQL安装目录
在CMD命令行窗口，进入MYSQL安装目录 比如 d:mysql\bin
```
cd mysql\bin
```


# 3、进入mysql安全模式
进入mysql安全模式，即当mysql起来后，不用输入密码就能进入数据库。
命令为： mysqld-nt --skip-grant-tables
(64 位系统输入 mysqld --skip-grant-tables)
```
mysqld-nt --skip-grant-tables
```


# 4、使用空密码的方式登录
重新打开一个CMD命令行窗口，输入mysql -uroot -p
使用空密码的方式登录MySQL（不用输入密码，直接按回车）
```
mysql -uroot -p
```



# 5、修改密码
输入以下命令开始修改root用户的密码（注意：命令中mysql.user中间有个“点”）
```
mysql> update mysql.user set password=PASSWORD("新密码") where User="root";
```

# 6、刷新权限表
```
mysql> flush privileges;
```



# 7、退出
```
mysql> quit 
```


这样MYSQL超级管理员账号 ROOT已经重新设置好了，接下来 在任务管理器里结束掉 mysql-nt.exe 这个进程，重新启动MYSQL即可！（也可以直接重新启动服务器）

MYSQL重新启动后，就可以用新设置的ROOT密码登陆MYSQL了！

