---
layout:     post
title:      SpringBoot 生产环境模板引入错误
subtitle:   Spring,Spring Boot
date:       2018-04-01
author:     Heiyogl
header-img: img/post-bg-home.jpg
catalog: true
tags:
    - JAVA
    - SpringBoot
---


# 生产环境引入模板报错
使用SpringBoot 推荐的模板引擎Thymeleaf时，在Eclipse中运行正常，但打成Jar包运行时，报错：
template might not exist or might not be accessible by any of the configured Template Resolvers

```
ERROR (TemplateEngine.java:1085)- [THYMELEAF][http-nio-8
080-exec-10] Exception processing template "/user/userlist": Error resolving tem
plate "/user/userlist", template might not exist or might not be accessible by a
ny of the configured Template Resolvers
2018-03-26 11:03:10,444 ERROR (DirectJDKLog.java:181)- Servlet.service() for ser
vlet [dispatcherServlet] in context with path [] threw exception [Request proces
sing failed; nested exception is org.thymeleaf.exceptions.TemplateInputException
: Error resolving template "/user/userlist", template might not exist or might n
ot be accessible by any of the configured Template Resolvers] with root cause
org.thymeleaf.exceptions.TemplateInputException: Error resolving template "/user
/userlist", template might not exist or might not be accessible by any of the co
nfigured Template Resolvers
        at org.thymeleaf.TemplateRepository.getTemplate(TemplateRepository.java:
246)
        at org.thymeleaf.TemplateEngine.process(TemplateEngine.java:1104)
        at org.thymeleaf.TemplateEngine.process(TemplateEngine.java:1060)
        at org.thymeleaf.TemplateEngine.process(TemplateEngine.java:1011)
        at org.thymeleaf.spring4.view.ThymeleafView.renderFragment(ThymeleafView
.java:335)
        at org.thymeleaf.spring4.view.ThymeleafView.render(ThymeleafView.java:19
0)
        at org.springframework.web.servlet.DispatcherServlet.render(DispatcherSe
rvlet.java:1286)
        at org.springframework.web.servlet.DispatcherServlet.processDispatchResu
lt(DispatcherServlet.java:1041)
        at org.springframework.web.servlet.DispatcherServlet.doDispatch(Dispatch
erServlet.java:984)
        at org.springframework.web.servlet.DispatcherServlet.doService(Dispatche
rServlet.java:901)

```



# 解决方案
#### 修改application.properties 文件
```
spring.thymeleaf.prefix=classpath:templates/
spring.thymeleaf.suffix=.html
spring.thymeleaf.mode=LEGACYHTML5
spring.thymeleaf.cache=false
```

#### 修改Controller类的返回路径
```
@RequestMapping("/user/list")
public String toList() {
    return "user/userlist";
}
```
> 其中：  
> return "/user/userlist"; //在eclipse中运行正常，打成jar找不到模板  
> return "user/userlist";//在eclipse中运行正常、Jar形式运行正常
