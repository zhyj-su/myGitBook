# 4-2 添加SSH依赖

### 添加SSH依赖

可以通过项目右键添加相应的依赖

也可以直接在pom.xml文件中添加

SSH框架需要添加的依赖如下:

```xml
<dependencies>
  <dependency>
  	<groupId>javax.servlet</groupId>
  	<artifactId>servlet-api</artifactId>
  	<version>2.5</version>
  	<scope>provided</scope>
  </dependency>
  <dependency>
  	<groupId>org.hibernate</groupId>
  	<artifactId>hibernate-core</artifactId>
  	<version>5.0.7.Final</version>
  </dependency>
  <dependency>
  	<groupId>org.springframework</groupId>
  	<artifactId>spring-core</artifactId>
  	<version>4.2.4.RELEASE</version>
  </dependency>
  <dependency>
  	<groupId>org.springframework</groupId>
  	<artifactId>spring-beans</artifactId>
  	<version>4.2.4.RELEASE</version>
  </dependency>
  <dependency>
  	<groupId>org.springframework</groupId>
  	<artifactId>spring-context</artifactId>
  	<version>4.2.4.RELEASE</version>
  </dependency>
  <dependency>
  	<groupId>org.springframework</groupId>
  	<artifactId>spring-orm</artifactId>
  	<version>4.2.4.RELEASE</version>
  </dependency>
  <dependency>
  	<groupId>org.springframework</groupId>
  	<artifactId>spring-test</artifactId>
  	<version>4.2.4.RELEASE</version>
  </dependency>
  <dependency>
  	<groupId>org.springframework</groupId>
  	<artifactId>spring-context-support</artifactId>
  	<version>4.2.4.RELEASE</version>
  </dependency> 
  <dependency>
  	<groupId>org.springframework</groupId>
  	<artifactId>spring-aop</artifactId>
  	<version>4.2.4.RELEASE</version>
  </dependency>
  <dependency>
  	<groupId>org.springframework</groupId>
  	<artifactId>spring-web</artifactId>
  	<version>4.2.4.RELEASE</version>
  </dependency>
  <dependency>
  	<groupId>org.aspectj</groupId>
  	<artifactId>aspectjweaver</artifactId>
  	<version>1.8.7</version>
  </dependency>
  <dependency>
  	<groupId>org.apache.struts</groupId>
  	<artifactId>struts2-core</artifactId>
  	<version>2.3.24</version>
  </dependency>
  <dependency>
  	<groupId>org.apache.struts</groupId>
  	<artifactId>struts2-spring-plugin</artifactId>
  	<version>2.3.24</version>
  </dependency>
  <dependency>
  	<groupId>mysql</groupId>
  	<artifactId>mysql-connector-java</artifactId>
  	<version>5.1.6</version>
  </dependency>
  <dependency>
  	<groupId>jstl</groupId>
  	<artifactId>jstl</artifactId>
  	<version>1.2</version>
  </dependency>
</dependencies>

```





