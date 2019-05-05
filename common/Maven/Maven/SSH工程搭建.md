# 4-3 SSH工程搭建

### SSH工程搭建

以一个简单的需求为例，测试SSH框架是否搭建成功

###### 需求

用ssh框架搭建一个工程，功能为单表的列表查询

| 字段名称 | 字段类型    | 字段含义    |
| ---- | ------- | ------- |
| uuid | bigint  | 主键:部门编号 |
| name | varchar | 部门名称    |
| tele | varchar | 部门电话    |

###### SQL代码

```sql
create database maventest;
use maventest;
create table dep(
	uuid bigint PRIMARY key,
	name varchar(20),
	tele varchar(20)
	);
```

###### 创建一个maven项目

​	packaging选择war

​	添加SSH相关依赖

###### 创建包结构

​	action

​	service

​	dao

###### 添加spring配置文件



###### 添加struts配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE struts PUBLIC
	"-//Apache Software Foundation//DTD Struts Configuration 2.3//EN"
	"http://struts.apache.org/dtds/struts-2.3.dtd">
<struts>
    <package name="default" namespace="/" extends="struts-default">    	   	
    </package>
</struts>

```

###### 修改web.xml文件

```xml
<listener>
	<listener-class>
      org.springframework.web.context.ContextLoaderListener
  	</listener-class>
	</listener>
	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>classpath*:applicationContext.xml</param-value>
	</context-param>
	<filter>
   		<filter-name>openSessionInView</filter-name>
		<filter-class>
     	 	org.springframework.orm.hibernate5.support.OpenSessionInViewFilter
    	 </filter-class>
	</filter>
	<filter-mapping>
		<filter-name>openSessionInView</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>
	<filter>
		<filter-name>struts2</filter-name>	
      <filter-class>
        org.apache.struts2.dispatcher.ng.filter.StrutsPrepareAndExecuteFilter
      </filter-class>
	</filter>
	<filter-mapping>
		<filter-name>struts2</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>
```

###### 编写实体类

```java
package com.maven.domain;
public class Dep {

	private Long uuid;//部门编号
	private String name;//部门名称
	private String tele;//部门电话
	
	public Long getUuid() {
		return uuid;
	}
	public void setUuid(Long uuid) {
		this.uuid = uuid;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public String getTele() {
		return tele;
	}
	public void setTele(String tele) {
		this.tele = tele;
	}
}
```

###### 编写映射文件

在resources路径下创建同名的domain路径

编写映射文件

```xml
<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE hibernate-mapping PUBLIC "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
"http://hibernate.sourceforge.net/hibernate-mapping-3.0.dtd">
<hibernate-mapping>
    <class name="com.maven.domain.Dep" table="dep" >
        <id name="uuid" >           
        </id>
        <property name="name" />    
        <property name="tele" />   
    </class>
</hibernate-mapping>
```

###### 编写action

```java
package com.maven.action;
import java.util.List;
import org.apache.struts2.ServletActionContext;
import com.maven.domain.Dep;
import com.maven.service.DepService;
import com.opensymphony.xwork2.ActionContext;

public class DepAction {
	
	private DepService depService;

	public DepService getDepService() {
		return depService;
	}
	public void setDepService(DepService depService) {
		this.depService = depService;
	}
	public String getList(){
		List<Dep> list=depService.getList();
		ServletActionContext.getRequest().setAttribute("list",list);
		return "success";
	}
}

```

###### 编写Service

```java
package com.maven.service.impl;
import java.util.List;
import com.maven.dao.DepDao;
import com.maven.domain.Dep;
import com.maven.service.DepService;

public class DepServiceImpl implements DepService {
	private DepDao depDao;

	public DepDao getDepDao() {
		return depDao;
	}
	public void setDepDao(DepDao depDao) {
		this.depDao = depDao;
	}
	@Override
	public List<Dep> getList() {
		return depDao.getList();
	}	
}
```

###### 编写Dao

```java
package com.maven.dao.impl;

import java.util.List;

import org.hibernate.SessionFactory;
import org.springframework.orm.hibernate5.support.HibernateDaoSupport;

import com.maven.dao.DepDao;
import com.maven.domain.Dep;

public class DepDaoImpl extends HibernateDaoSupport implements DepDao  {
	public void setSuperSessionFactory(SessionFactory sessionFactory){
		super.setSessionFactory(sessionFactory);
	}

	@Override
	public List<Dep> getList() {
		return (List<Dep>) this.getHibernateTemplate().find("from Dep");
		
	}
}
```

###### 添加Struts.xml配置信息

```xml
<package name="default" namespace="/" extends="struts-default"> 
    	<action name="dep_*" class="depAction" method="{1}">
    		<result name="success">/list.jsp</result>
    	</action>   	   	
</package>
```

###### 添加applicationContext.xml配置信息

```java
	<bean id="depAction" class="com.maven.action.DepAction">
		<property name="depService" ref="depService"></property>
	</bean>
	<bean id="depService" class="com.maven.service.impl.DepServiceImpl">
		<property name="depDao" ref="depDao"></property>
	</bean>
	<bean id="depDao" class="com.maven.dao.impl.DepDaoImpl">
		<property name="sessionFactory" ref="sessionFactory"></property>
	</bean>
```





​	