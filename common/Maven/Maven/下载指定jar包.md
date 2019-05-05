# 3-6 下载指定jar包

需要下载指定jar包而不想在工程中添加依赖进行下载

####  步骤 
新建一个pom文件

```xml
<?xml version="1.0"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>temp.download</groupId>
    <artifactId>temp-download</artifactId>
    <version>1.0-SNAPSHOT</version> 

    <dependencies>
<!-- 需要下载什么jar包 添加相应依赖 其余部分无需在意-->
<!-- https://mvnrepository.com/artifact/com.thoughtworks.xstream/xstream -->
<dependency>
    <groupId>com.thoughtworks.xstream</groupId>
    <artifactId>xstream</artifactId>
    <version>1.4.10</version>
</dependency>

    </dependencies>

</project>
```

新建download.bat

```xml
call mvn -f pom.xml dependency:copy-dependencies
```

需要下载特定jar包时,替换maven依赖并运行download.bat,下载完的jar包存放在当前路径target\dependency下

