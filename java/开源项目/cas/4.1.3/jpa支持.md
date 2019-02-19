# 添加jpa支持

参考:

<https://apereo.github.io/cas/4.1.x/installation/JPA-Ticket-Registry.html>



修改webapp/WEB-INF/spring-configuration/ticketRegistry.xml

```xml
<bean
      id="dataSource"
      class="com.mchange.v2.c3p0.ComboPooledDataSource"
      p:driverClass="${database.driverClass:org.hsqldb.jdbcDriver}"
      p:jdbcUrl="${database.url:jdbc:hsqldb:mem:cas-ticket-registry}"
      p:user="${database.user:sa}"
      p:password="${database.password:}"
      p:initialPoolSize="${database.pool.minSize:6}"
      p:minPoolSize="${database.pool.minSize:6}"
      p:maxPoolSize="${database.pool.maxSize:18}"
      p:maxIdleTimeExcessConnections="${database.pool.maxIdleTime:1000}"
      p:checkoutTimeout="${database.pool.maxWait:2000}"
      p:acquireIncrement="${database.pool.acquireIncrement:16}"
      p:acquireRetryAttempts="${database.pool.acquireRetryAttempts:5}"
      p:acquireRetryDelay="${database.pool.acquireRetryDelay:2000}"
      p:idleConnectionTestPeriod="${database.pool.idleConnectionTestPeriod:30}"
      p:preferredTestQuery="${database.pool.connectionHealthQuery:select 1}"
      />

<bean id="ticketRegistry" class="org.jasig.cas.ticket.registry.JpaTicketRegistry" />

<bean class="org.springframework.orm.jpa.support.PersistenceAnnotationBeanPostProcessor"/>

<util:list id="packagesToScan">
    <value>org.jasig.cas.ticket</value>
    <value>org.jasig.cas.adaptors.jdbc</value>
</util:list>

<bean class="org.springframework.orm.jpa.vendor.HibernateJpaVendorAdapter"
      id="jpaVendorAdapter"
      p:generateDdl="true"
      p:showSql="true" />

<bean id="entityManagerFactory"
      class="org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean"
      p:dataSource-ref="dataSource"
      p:jpaVendorAdapter-ref="jpaVendorAdapter"
      p:packagesToScan-ref="packagesToScan">
    <property name="jpaProperties">
        <props>
            <prop key="hibernate.dialect">${database.dialect:org.hibernate.dialect.HSQLDialect}</prop>
            <prop key="hibernate.hbm2ddl.auto">create-drop</prop>
            <prop key="hibernate.jdbc.batch_size">${database.batchSize:1}</prop>
        </props>
    </property>
</bean>

<bean id="transactionManager" class="org.springframework.orm.jpa.JpaTransactionManager"
      p:entityManagerFactory-ref="entityManagerFactory" />

<tx:advice id="txCentralAuthenticationServiceAdvice" transaction-manager="transactionManager">
    <tx:attributes>
        <tx:method name="destroyTicketGrantingTicket" read-only="false" />
        <tx:method name="grantServiceTicket" read-only="false" />
        <tx:method name="delegateTicketGrantingTicket" read-only="false" />
        <tx:method name="validateServiceTicket" read-only="false" />
        <tx:method name="createTicketGrantingTicket" read-only="false" />

        <tx:method name="getTicket" read-only="false" />
        <tx:method name="getTickets" read-only="true" />
    </tx:attributes>
</tx:advice>

<tx:advice id="txRegistryAdvice" transaction-manager="transactionManager">
    <tx:attributes>
        <tx:method name="deleteTicket" read-only="false" />
        <tx:method name="addTicket" read-only="false" />
        <tx:method name="updateTicket" read-only="false" />
        <tx:method name="getTicket" read-only="true" />
        <tx:method name="getTickets" read-only="true" />
        <tx:method name="sessionCount" read-only="true" />
        <tx:method name="serviceTicketCount" read-only="true" />
    </tx:attributes>
</tx:advice>

<tx:advice id="txRegistryServiceTicketDelegatorAdvice" transaction-manager="transactionManager">
    <tx:attributes>
        <tx:method name="grantTicketGrantingTicket" read-only="false" />
    </tx:attributes>
</tx:advice>

<tx:advice id="txRegistryTicketGrantingTicketDelegatorAdvice" transaction-manager="transactionManager">
    <tx:attributes>
        <tx:method name="markTicketExpired" read-only="false" />
        <tx:method name="grantServiceTicket" read-only="false" />
    </tx:attributes>
</tx:advice>

<tx:advice id="txRegistryLockingAdvice" transaction-manager="transactionManager">
    <tx:attributes>
        <tx:method name="getOwner" read-only="true" />
        <tx:method name="acquire" read-only="false" />
        <tx:method name="release" read-only="false" />
    </tx:attributes>
</tx:advice>

<aop:config>
    <aop:pointcut id="ticketRegistryOperations" expression="execution(* org.jasig.cas.ticket.registry.JpaTicketRegistry.*(..))"/>
    <aop:pointcut id="ticketRegistryLockingOperations" expression="execution(* org.jasig.cas.ticket.registry.support.JpaLockingStrategy.*(..))"/>
    <aop:pointcut id="ticketRegistryServiceTicketDelegatorOperations" expression="execution(* org.jasig.cas.ticket.registry.AbstractDistributedTicketRegistry$ServiceTicketDelegator.*(..))"/>
    <aop:pointcut id="ticketRegistryTicketGrantingTicketDelegatorOperations" expression="execution(* org.jasig.cas.ticket.registry.AbstractDistributedTicketRegistry$TicketGrantingTicketDelegator.*(..))"/>
    <aop:pointcut id="casOperations" expression="execution(* org.jasig.cas.CentralAuthenticationServiceImpl.*(..))"/>

    <aop:advisor advice-ref="txRegistryAdvice" pointcut-ref="ticketRegistryOperations"/>
    <aop:advisor advice-ref="txRegistryLockingAdvice" pointcut-ref="ticketRegistryLockingOperations"/>
    <aop:advisor advice-ref="txRegistryTicketGrantingTicketDelegatorAdvice" pointcut-ref="ticketRegistryTicketGrantingTicketDelegatorOperations"/>
    <aop:advisor advice-ref="txRegistryServiceTicketDelegatorAdvice" pointcut-ref="ticketRegistryServiceTicketDelegatorOperations"/>
    <aop:advisor advice-ref="txCentralAuthenticationServiceAdvice" pointcut-ref="casOperations"/>
</aop:config>


<bean id="ticketRegistryCleaner"
      class="org.jasig.cas.ticket.registry.support.DefaultTicketRegistryCleaner"
      c:centralAuthenticationService-ref="centralAuthenticationService"
      c:ticketRegistry-ref="ticketRegistry"
      p:lock-ref="cleanerLock"/>

<bean id="cleanerLock" class="org.jasig.cas.ticket.registry.support.JpaLockingStrategy"
      p:uniqueId="${host.name}"
      p:applicationId="cas-ticket-registry-cleaner" />

<bean id="jobDetailTicketRegistryCleaner"
      class="org.springframework.scheduling.quartz.MethodInvokingJobDetailFactoryBean"
      p:targetObject-ref="ticketRegistryCleaner"
      p:targetMethod="clean" />

<bean id="triggerJobDetailTicketRegistryCleaner"
      class="org.springframework.scheduling.quartz.SimpleTriggerFactoryBean"
      p:jobDetail-ref="jobDetailTicketRegistryCleaner"
      p:startDelay="20000"
      p:repeatInterval="5000000" />
```



注意点:

```xml
<bean id="entityManagerFactory"
      class="org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean"
      p:dataSource-ref="dataSource"
      p:jpaVendorAdapter-ref="jpaVendorAdapter"
      p:packagesToScan-ref="packagesToScan">
    <property name="jpaProperties">
        <props>
            <prop key="hibernate.dialect">${database.dialect:org.hibernate.dialect.HSQLDialect}</prop>
            <prop key="hibernate.hbm2ddl.auto">create-drop</prop>
            <prop key="hibernate.jdbc.batch_size">${database.batchSize:1}</prop>
        </props>
    </property>
</bean>
```

修改jpaProoerties参数信息:

-  这里使用的是mysql数据库,其它数据库参数可自行查询
- hibernate.dialect数据库方言修改为org.hibernate.dialect.MySQLDialect
- hibernate.hbm2ddl.auto默认值为create-drop,启动时创建表结构,退出时删除表结构(推荐update)



jpa会自动创建locks、serviceticket和ticketgrantingticket三张表

如果hibernate.hbm2ddl.auto设置为none,可手动创建这三张表

创建的sql语句如下:

```sql
CREATE TABLE `locks` (
`application_id` varchar(255) NOT NULL,
`expiration_date` datetime DEFAULT NULL,
`unique_id` varchar(255) DEFAULT NULL,
PRIMARY KEY (`application_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8

create table TICKETGRANTINGTICKET (
ID varchar(255) not null, 
NUMBER_OF_TIMES_USED integer, 
CREATION_TIME bigint, 
EXPIRATION_POLICY longblob not null,
LAST_TIME_USED bigint,
PREVIOUS_LAST_TIME_USED bigint,
AUTHENTICATION longblob not null,
EXPIRED boolean not null,
PROXIED_BY varbinary(255), 
SERVICES_GRANTED_ACCESS_TO longblob not null, 
SUPPLEMENTAL_AUTHENTICATIONS longblob not null,
ticketGrantingTicket_ID varchar(255), 
primary key (ID))

create table SERVICETICKET (
ID varchar(255) not null, 
NUMBER_OF_TIMES_USED integer, 
CREATION_TIME bigint, 
EXPIRATION_POLICY longblob not null, 
LAST_TIME_USED bigint, 
PREVIOUS_LAST_TIME_USED bigint, 
FROM_NEW_LOGIN boolean not null, 
TICKET_ALREADY_GRANTED boolean not null, 
SERVICE longblob not null, 
ticketGrantingTicket_ID varchar(255), 
primary key (ID))
```



查看索引

```sql
show index from SERVICETICKET where column_name='ticketGrantingTicket_ID'; 
show index from TICKETGRANTINGTICKET where column_name='ticketGrantingTicket_ID';
```



创建索引

```sql
CREATE INDEX ST_TGT_FK_I ON SERVICETICKET (ticketGrantingTicket_ID);
CREATE INDEX ST_TGT_FK_I ON TICKETGRANTINGTICKET (ticketGrantingTicket_ID);
```

