# 4-1 创建war工程

### 创建war工程

新建一个maven project

packaging选择war

![创建一个war项目](images\创建一个war项目.png)

此时，一个war项目即创建完成

![创建一个war项目](images\war项目.png)

此时，项目报错

查看报错原因

![创建一个war项目](images\war报错.png)

报错原因是缺少web.xml文件

将web.xml文件拷贝至webapp/WEB-INF目录下

即可



### 添加插件

add Plugin

需要添加tomcat插件

compile插件(更改jdk版本)



或者直接通过复制修改好的pom.xml文件

```xml
 <build>  
	  <plugins>
	      <plugin>  
	          <groupId>org.apache.maven.plugins</groupId>  
	          <artifactId>maven-compiler-plugin</artifactId>  
	          <version>2.3.2</version>  
	          <configuration>  
	              <source>1.7</source>  
	              <target>1.7</target>  
	          </configuration>  
	      </plugin>  
	      <plugin>
				<groupId>org.apache.tomcat.maven</groupId>
				<artifactId>tomcat7-maven-plugin</artifactId>
				<configuration>
					<!-- 指定端口 -->
					<port>8080</port>
					<!-- 请求路径 -->
					<path>/</path>
				</configuration>
	  	  </plugin>
	  </plugins>  
	</build>

```

然后update project即可



### 添加servlet-Api依赖

因为我们创建的是war工程 ,需要response 和request 等对象，而这些对象是在servlet-api中的，所以我们需要添加servlet-api ,才能保证工程正常编译

可以通过添加依赖或者直接在pom.xml中添加

```xml
<dependencies>
  	<dependency>
  		<groupId>javax.servlet</groupId>
  		<artifactId>servlet-api</artifactId>
  		<version>2.5</version>
	    <scope>provided</scope>
  	</dependency>
  </dependencies>
```

注意:必须制定scope为provided，这是设置依赖范围

添加后更新工程



### 测试

添加index.jsp页面

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
<title>maven</title>
</head>
<body>
	<h1>Maven...</h1>
</body>
</html>
```

启动tomcat

run as-Maven build...

输入命令tomcat7:run![启动截图](images\启动截图.png)

访问网页

 ![web访问](images\web访问.png)

























