---
title: Spring配置Bean时使用外部属性文件(比如配置数据库源)
author: YangChongZhi
tags: [Spring,Java]
date: 2020-03-08 14:57:36
id: 1
categories: Spring
---
## 引言：
Spring在配置某些Bean时可以使用外部的属性文件。使用外部化属性文件中的属性，在之后开发过程中便于修改，只需修改外部的属性文件即可。比如配置数据库时。
- Main.java
- beans-properties.xml
- db.properties
<!-- more -->

### 需要在工程中添加如下的“.jar”包文件
- [c3p0-0.9.5.5.jar](c3p0-0.9.5.5.jar)
- [mysql-connector-java-8.0.18.jar](mysql-connector-java-8.0.18.jar)
- [mchange-commons-java-0.2.19.jar](mchange-commons-java-0.2.19.jar)


### Main.java
```java
package com.yczlab.spring.beans.properties;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import javax.sql.DataSource;
import java.sql.SQLException;

public class Main {
    public static void main(String[] args) throws SQLException {
        ApplicationContext context = new ClassPathXmlApplicationContext("beans-properties.xml");

        DataSource dataSource = (DataSource) context.getBean("dataSource");
        System.out.println(dataSource.getConnection());

        System.out.println("*********************");
        DataSource dataSource1 = (DataSource) context.getBean("dataSource1");
        System.out.println(dataSource1);

    }
}
```

### beans-properties.xml（src/目录下）
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 导入属性文件 -->
    <context:property-placeholder location="classpath:db.properties"/>

    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <!--传统方式配置，在之后开发过程中的修改很麻烦-->
        <property name="user" value="root"/><!--实际的数据库用户名-->
        <property name="password" value="123456"/><!--实际的数据库密码-->
        <property name="driverClass" value="com.mysql.cj.jdbc.Driver"/>
        <property name="jdbcUrl" value="jdbc:mysql:///spring_learning_test?serverTimezone=UTC"/><!--实际的数据库名-->
    </bean>

    <bean id="dataSource1" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <!--使用外部化属性文件的属性，在之后开发过程中便于修改，只需修改外部的属性文件即可-->
        <property name="user" value="${user}"/>
        <property name="password" value="${password}"/>
        <property name="driverClass" value="${driverClass}"/>
        <property name="jdbcUrl" value="${jdbcUrl}"/>
    </bean>

</beans>
```

### db.properties（src/目录下）
```
# 实际的数据库用户名
user=root
# 实际的数据库密码
password=123456
driverClass=com.mysql.cj.jdbc.Driver
# 实际的数据库名
jdbcUrl=jdbc:mysql:///spring_learning_test?serverTimezone=UTC
```

