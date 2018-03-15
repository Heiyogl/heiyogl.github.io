---
layout:     post
title:      SpringBoot入门注意事项
date:       2018-03-14
author:     Heiyogl
header-img: img/post-bg-home.jpg
catalog: true
tags:
    - JAVA
    - SpringBoot
---

# 层级目录
略


# 常用注解说明
## 控制器主体 Controller
- @RestController  
对整个controller里面的**所有方法**都以json格式输出，不需要在对方法的返回结果做Json转换。适合应用于 RESTful API 开发工程。

- @Controller  
最常用的控制器注解。与@RestController 的区别在于@Controller下的方法返回值有方法注解控制可以跳转页面，也可以做Ajax请求返回Json数据。

- @RequestMapping  
用来处理地址映射请求（URL路由信息，页面跳转）。

```
@Controller
public class UserController {

}
```

## 控制器方法
- @RequestMapping("/user/list") //页面跳转
```
@RequestMapping("/user/list") //接口请求地址 http://127.0.0.1:8080/user/list
public String toList() {
    return "/user/userlist";//页面跳转：跳转到用户列表页 /user/userlist.html
}
```


- @ResponseBody //Ajax 响应  
> 其中Ajax请求地址为：/user.lists，POST 方式请求  
> @RequestBody HashMap<String, Object> param  将页面参数直接封装到Map 计划中
```
@ResponseBody
@RequestMapping(value = "/user.lists", method = RequestMethod.POST)
@LoggerManage(description = "用户列表接口")
public ResponseData getList(@RequestBody HashMap<String, Object> param){
    int pageNo = Convert.getInteger(null == param.get("pageNo") ? "0" : param.get("pageNo"));
    int pageSize = Convert.getInteger(null == param.get("pageSize") ? "0" : param.get("pageSize"));
return userService.queryUser(param, pageNo, pageSize);
}
```


## 实体类 Entity
> @Entity  //注解这是个实体类  
> @Table(name="t_user")   //设置数据库中表名字，不配置时，表名默认与实体类名字一致

```
@Entity  
@Table(name="t_user")
public class User {

}
```
> @Id //ID 主键  
> @GeneratedValue //ID 主键自增长

```
@Id
@GeneratedValue
private Long id;
```

> @Column //字段特性、名称等标识  

```
//主键表ID
@Column(nullable = false)
private String mid;

//创建时间
@Column(nullable = false)
private Timestamp createdTime;
```

> @Transient //标识该字段并非数据库字段映射  

```
@Transient//不映射成列的字段
private String lN;//登录时传输的登录名
```

## 资源引入
```
@Autowired
private UserService userService;

@Autowired
private EntityManager entityManager;
```



## 启动类
- @SpringBootApplication
- @EnableScheduling//启用定时任务



# 启动类
启动类必须存放于项目的根目录，且整个项目有且只有一个启动类（只能有一个带Main方法的类）
```
@SpringBootApplication
public class SpbootApplication {
    public static void main(String[] args) {
    	SpringApplication.run(SpbootApplication.class, args);
	}
}
```
> PS：当项目中存在多个Main方法是，程序会报找不到启动类的错误。
![image](https://note.youdao.com/yws/public/resource/4b19c3a22105e4645ef4c1296fe78446/xmlnote/1682C419EFEC4A8DABCE6018E18EF33D/22325)



# 配置定时任务
- 在启动类中添加类注解 @EnableScheduling
```
@SpringBootApplication
@EnableScheduling//启用定时任务
public class SpbootApplication {
    public static void main(String[] args) {
    	SpringApplication.run(SpbootApplication.class, args);
	}
}
```

- 在定时任务实现类中添加时间参数配置
```
@Scheduled(fixedDelay = 5000)//上一次执行完毕时间点之后5秒再执行
//@Scheduled(fixedRate = 5000)//上一次开始执行时间点之后5秒再执行
//@Scheduled(cron = "*/5 * * * * ?")// cron 表达式
private void doWork() {

}
```


pom.xml 配置文件引入 springboot starter 包。  
PS：多个定时任务默认是窜行的方式运行，定时任务并行需要在 resources 目录下添加 applicationContext.xml 配置文件，并在根目录下添加配置文件解析器 XmlImportingConfiguration.java ，文件内容具体如下：
## applicationContext.xml
```
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
    xmlns:task="http://www.springframework.org/schema/task"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/task http://www.springframework.org/schema/task/spring-task-3.0.xsd">


    <!-- Enables the Spring Task @Scheduled programming model -->
    <task:executor id="executor" pool-size="5" />
    <task:scheduler id="scheduler" pool-size="2" />
    <task:annotation-driven executor="executor" scheduler="scheduler" />


</beans>
```

## XmlImportingConfiguration.java
```
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.ImportResource;

@Configuration
@ImportResource("classpath:applicationContext.xml")
public class XmlImportingConfiguration {

}
```


# Logback 日志配置
Spring Boot 支持多种日志输出组件，这里介绍Logback的配置方式
- pom.xml 引入依赖包
```
<dependency>
	<groupId>com.googlecode.log4jdbc</groupId>
	<artifactId>log4jdbc</artifactId>
	<version>1.2</version>
</dependency>
```
- logback.xml 配置（resources根目录）
```
<?xml version="1.0"?>
<configuration>
	<!-- 控制台输出: %m输出的信息,%p日志级别,%t线程名,%d日期,%c类的全名,,,, -->
	<appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
		<encoder>
			<pattern>%d %p (%file:%line\)- %m%n</pattern>
			<charset>UTF-8</charset>
		</encoder>
	</appender>
	<appender name="baselog"
		class="ch.qos.logback.core.rolling.RollingFileAppender">
		<File>log/base.log</File>
		<rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
			<fileNamePattern>log/base.log.%d.%i</fileNamePattern>
			<timeBasedFileNamingAndTriggeringPolicy
				class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
				<!-- or whenever the file size reaches 64 MB -->
				<maxFileSize>64 MB</maxFileSize>
			</timeBasedFileNamingAndTriggeringPolicy>
		</rollingPolicy>
		<encoder>
			<pattern>
				%d %p (%file:%line\)- %m%n
			</pattern>
			<!-- 设置字符集 -->
			<charset>UTF-8</charset>
		</encoder>
	</appender>
	<!-- show parameters for hibernate sql 专为 Hibernate 定制 -->
	<!-- <logger name="org.hibernate.SQL" level="DEBUG">
		<appender-ref ref="baselog" />
		<appender-ref ref="STDOUT" />
	</logger>
	<logger name="org.hibernate.type.descriptor.sql.BasicBinder" level="TRACE">
		<appender-ref ref="baselog" />
		<appender-ref ref="STDOUT" />
	</logger> -->
	<root level="info">
		<appender-ref ref="STDOUT" />
	</root>
	<logger name="com.izhonghong.spboot" level="DEBUG">
		<appender-ref ref="baselog" />
	</logger>
</configuration>
```
- 代码调用
```
logger.debug("login failed log ");
```


# 动态SQL
- 引入实体管理器 EntityManager
```
@Autowired
private EntityManager entityManager;
```

- 通过动态SQL获取查询结果集（其中，sql 为动态拼装后的sql语句）
```
List<Object[]> list = entityManager.createNativeQuery(sql).getResultList();
```



# URI命名规则
- 页面跳转
```
@RequestMapping("/users")
public String toUsers() {
    return "/user/userlist";
}
```
- Ajax 方法请求
```
url : "/user.lists"
```


# Thymeleaf 获取Java 后端变量
## 获取 session、request参数
- 页面头部引用
```
<html xmlns:th="http://www.thymeleaf.org"
      xmlns:sec="http://www.thymeleaf.org/thymeleaf-extras-springsecurity3">
```

- 调用示例
```
<span th:if="${session.login_user !=null}">
    <span th:text="${session.login_user.loginName}"></span>
</span>
```
