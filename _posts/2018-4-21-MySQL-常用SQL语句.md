---
layout:     post
title:      MySQL-常用SQL语句
subtitle:   MySQL,常用SQL语句 
date:       2018-04-21
author:     Heiyogl
header-img: img/post-bg-home.jpg
catalog: true
tags:
    - MySQL
---

# 指定顺序Order by
mysql 按照指定字段的指定值排序：
```
select * from t_user order by FIELD(id, 0, 2, 11, 1, 3, 4, 5, 6, 8, 9, 10) asc
```


# 逗号分隔查询 FIND_IN_SET
使用FIND_IN_SET(str,sbstr)函数查询 “,”  号（逗号）隔开的数据;  
filed 字段存储形式为多个逗号隔开的值：1,2,3,5,6
```
SELECT * from t_user  where FIND_IN_SET('1',filed)
```


# 日期处理 ADDTIME interval
修改时间的年月，其他保留不变
```
select t.created_at,ADDTIME (CONCAT("2014-02-",DAYOFMONTH(t.created_at))+interval 0 hour,time(t.created_at)) as newtime
from t_user t where t.id=1
```

# 检查、修复表 CHECK REPAIR
```
-- 检查表,获得不是OK就是有问题需要修复。
check table t_user;

-- 修复表
REPAIR TABLE `t_user`;
```

# 计算指定日期为第几个星期 WEEKOFYEAR
通过日期 计算是第几个星期
```
select WEEKOFYEAR(ADDDATE('2012-01-29',1));-- 从星期天开始算第一天
select WEEKOFYEAR(NOW());-- 从星期一开始算第一天(其中：now() 为计算当前日期的函数)
```

# A表查询结果，更新B表 update from
通过另外一个表查询结果来更新表 update from
```
update b_gem gem inner join b_productlist list set gem.status='NEW',gem.storeroomid=list.storeroom where list.type=1 and list.sid=2 and list.serid=gem.serId
```

# 两个字段合并成一个字段显示 concat
将查询结果中的2个字段放到合并到一个字段显示
```
select concat(first_name,' ',last_name) as customerName from t_agmt_customer 
```

# 获取表结构信息 show full fields
```
show full fields from t_user;
```


# 查看表的索引大小 Total Index Size
```
-- 将选择 information_schema 数据库 
SELECT CONCAT(ROUND(SUM(index_length)/(1024*1024), 2), ' MB') AS 'Total Index Size' FROM TABLES  WHERE table_schema = 'mytable_test' and table_name='t_user';

```


# mysql 数据导入导出 快速
```
-- 导出到指定文件
select * into outfile 'd:/DataExport/t_user.txt' from t_user;

-- 从指定文件导入数据
load data infile 'd:/DataExport/t_user.txt' into table t_user;
load data infile 'D:/databak/t_user.txt' into table t_status CHARACTER SET utf8;//中文乱码处理


load data infile 'd:/DataExport/t_user.txt' replace into table t_user;

-- 导入到指定列
load data infile 'd:/temp/Online/t_user.txt'
into table t_user 
FIELDS TERMINATED BY '	'
LINES TERMINATED BY '\n'
(id,mid,province,city);

-- 更多用法请参考  http://www.cnblogs.com/mybi/archive/2012/10/05/2711990.html 


LOAD DATA [LOW_PRIORITY] [LOCAL] INFILE 'file_name.txt' [REPLACE | IGNORE]
    INTO TABLE tbl_name
    [FIELDS
        [TERMINATED BY '\t']
        [OPTIONALLY] ENCLOSED BY '']
        [ESCAPED BY '\\' ]]
    [LINES TERMINATED BY '\n']
    [IGNORE number LINES]
    [(col_name,...)]

```

# 内容替换 replace
```
update t_user set url = replace(url,"localhost","127.0.0.1") where id=1
```

# 缓存查看与设置
```
show variables like '%query_cache%';	-- 查看cache的设置
show status like '%Qcache%';	-- 性能监控

FLUSH QUERY CACHE; -- 清理查询缓存碎片以提高内存使用性能。该语句不从缓存中移出任何查询。
RESET QUERY CACHE; -- 从查询缓存中移出所有查询。FLUSH TABLES语句也执行同样的工作。
```

# 给用户赋值所有权限 grant
```
grant all on mytable.* to user@'10.111.1.%' identified by 'user';
flush PRIVILEGES;
```

# 存在该记录则更新，不存在则插入 DUPLICATE
```
insert t_user (name,age) values ('fdasf23',2) ON DUPLICATE KEY UPDATE age=age+3

若存在唯一索引冲突时，则修改 age 字段；若不存在则新增；
```



# 带分页的存储过程 存储过程实例
```

DELIMITER $$

DROP PROCEDURE IF EXISTS `test`.`test_proc`$$

CREATE PROCEDURE `test`.`test_proc` (IN i_name VARCHAR(100),IN i_pageIndex INT, IN i_pageSize INT)
BEGIN

/*
call test_proc('a',1, 3)  -- 调用示例
*/
declare stmt varchar(2000);

DROP TABLE IF EXISTS temp_tb1;
create temporary table temp_tb1(
	id int(11) NOT NULL AUTO_INCREMENT,
	myname varchar(50),
	PRIMARY KEY (`id`)
);


insert into temp_tb1(myname)values('aa'),('abb'),('acc'),('add'),('aee');

set @sql = concat('select * from temp_tb1 where myname like ''%',i_name,'%'' limit ',(i_pageIndex-1) * i_pageSize,',',i_pageSize); -- 注意like后两个单引号表示一个。
prepare stmt from @sql;  
execute stmt;  


END$$

DELIMITER ;


```

