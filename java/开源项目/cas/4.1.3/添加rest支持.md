# 添加rest支持

添加maven依赖

```xml
<!--cas-server-support-rest-->
<dependency>
    <groupId>org.jasig.cas</groupId>
    <artifactId>cas-server-support-rest</artifactId>
    <version>${project.version}</version>
</dependency>
```

web.xml配置(官方已经配置,只需添加maven依赖即可)

```xml
<!-- REST support if cas-server-support-rest is included -->
<servlet-mapping>
    <servlet-name>cas</servlet-name>
    <url-pattern>/v1/*</url-pattern>
</servlet-mapping>
```

测试:

http://ip:port/casName/v1/tickets

可以通过浏览器或者curl进行测试

状态码:405 Method NOT ALLOWED代表成功添加了rest支持

