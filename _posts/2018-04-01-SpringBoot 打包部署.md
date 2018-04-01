---
layout:     post
title:      SpringBoot 打包部署
subtitle:   SpringBoot
date:       2018-04-01
author:     Heiyogl
header-img: img/post-bg-home.jpg
catalog: true
tags:
    - JAVA
    - SpringBoot
---


# 自定义配置文件
SpringBoot 以Jar方式打包运行时，通常会把配置文件放到Jar外面，目的是为了方便部署时调整相关配置。针对此场景以及不想使用application.properties作为配置文件时，我们可以在启动类中使用  @PropertySource 指定配置文件。
> 首先，在项目中新建 config **文件夹**，将 config.conf 配置文件放到config文件夹中

```
@SpringBootApplication
@EnableScheduling//启用定时任务
@PropertySource(value={"file:config/config.conf"})//指定配置文件，不使用默认的application.properties
public class StartApplication {
	public static void main(String[] args) {
		SpringApplication.run(StartApplication.class, args);
	}
}
```


# 创建启动批处理程序 startup.bat
```
@echo off
title SpbootTest-Jar
java -jar spboot-project.jar
pause
```

# 文件目录
```
jar-bat
    - startup.bat
    - spboot-project.jar
    - config
        - config.conf
```

# pom.xml 文件配置 程序入口
> <mainClass>com.heiyogl.spboot.StartApplication</mainClass>

```
<build>
		<finalName>MyProject</finalName>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
				<!-- 开发环境的调试>> -->
				<configuration>
					<fork>true</fork>
					<mainClass>com.heiyogl.spboot.StartApplication</mainClass><!-- 打包Jar文件，标明程序入口 -->
				</configuration>
			</plugin>
			
			
			<!-- 将引用到的jar导出到编译目录 BEGIN -->
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-jar-plugin</artifactId>
				<configuration>
					<archive>
						<manifest>
							<addClasspath>true</addClasspath>
							<classpathPrefix>lib/</classpathPrefix>
							<mainClass></mainClass>
						</manifest>
					</archive>
					
					<!-- 打包排除配置 -->
					<excludes>
						<exclude>**/*.conf</exclude><!-- 打包时，排除  .conf 的配置文件 -->
						<exclude>**/*.txt</exclude><!-- 打包时，排除  .txt 的配置文件 -->
						<!-- <exclude>logback.xml</exclude> 打包时，排除  logback.xml配置文件 -->
					</excludes>
					
				</configuration>
			</plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-dependency-plugin</artifactId>
				<executions>
					<execution>
						<id>copy</id>
						<phase>package</phase>
						<goals>
							<goal>copy-dependencies</goal>
						</goals>
						<configuration>
							<outputDirectory>${project.build.directory}/lib</outputDirectory>
						</configuration>
					</execution>
				</executions>
			</plugin>
			<!-- END -->
			
		</plugins>
	</build>
```

