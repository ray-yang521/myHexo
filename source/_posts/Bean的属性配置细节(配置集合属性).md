---
title: Bean的属性配置细节(配置集合属性)
author: YangChongZhi
tags: [Spring,Java]
date: 2020-03-07 19:07:05
id: 3
categories: Spring
---
## 引言：
为Spring中的Bean配置集合属性。
- Car.java
- ListPerson.java
- MapPerson.java
- DataSource.java
- Main.java
- applicationContext.xml
<!-- more -->

### Car.java
```java
package com.yczlab.spring.beans;

public class Car {
    private String brand;
    private String corp;
    private double price;
    private int maxSpeed;

    public Car(String brand, String corp, double price) {
        super();
        this.brand = brand;
        this.corp = corp;
        this.price = price;
    }
    public Car(String brand, String corp, int maxSpeed) {
        this.brand = brand;
        this.corp = corp;
        this.maxSpeed = maxSpeed;
    }

    public String getBrand() {
        return brand;
    }
    public void setBrand(String brand) {
        this.brand = brand;
    }
    public String getCorp() {
        return corp;
    }
    public void setCorp(String corp) {
        this.corp = corp;
    }
    public double getPrice() {
        return price;
    }
    public void setPrice(double price) {
        this.price = price;
    }
    public int getMaxSpeed() {
        return maxSpeed;
    }
    public void setMaxSpeed(int maxSpeed) {
        this.maxSpeed = maxSpeed;
    }

    @Override
    public String toString() {
        return "Car{" +
                "brand='" + brand + '\'' +
                ", corp='" + corp + '\'' +
                ", price=" + price +
                ", maxSpeed=" + maxSpeed +
                '}';
    }
}
```

### ListPerson
```java
package com.yczlab.spring.beans.collections;

import com.yczlab.spring.beans.Car;

import java.util.List;

public class ListPerson {
    private String name;
    private int age;

    private List<Car> cars;

    public ListPerson() {
    }
    public ListPerson(String name, int age, List<Car> cars) {
        this.name = name;
        this.age = age;
        this.cars = cars;
    }

    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public int getAge() {
        return age;
    }
    public void setAge(int age) {
        this.age = age;
    }
    public List<Car> getCars() {
        return cars;
    }
    public void setCars(List<Car> cars) {
        this.cars = cars;
    }

    @Override
    public String toString() {
        return "ListPerson{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", cars=" + cars +
                '}';
    }
}
```

### MapPerson
```java
package com.yczlab.spring.beans.collections;

import com.yczlab.spring.beans.Car;

import java.util.Map;

public class MapPerson {
    private String name;
    private int age;

    private Map<String, Car> cars;

    public MapPerson() {
    }
    public MapPerson(String name, int age, Map<String, Car> cars) {
        this.name = name;
        this.age = age;
        this.cars = cars;
    }

    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public int getAge() {
        return age;
    }
    public void setAge(int age) {
        this.age = age;
    }
    public Map<String, Car> getCars() {
        return cars;
    }
    public void setCars(Map<String, Car> cars) {
        this.cars = cars;
    }

    @Override
    public String toString() {
        return "MapPerson{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", cars=" + cars +
                '}';
    }
}
```

### DataSource
```java
package com.yczlab.spring.beans.collections;

import java.util.Properties;

public class DataSource {
    private Properties properties;

    public DataSource() {
    }
    public DataSource(Properties properties) {
        this.properties = properties;
    }

    public Properties getProperties() {
        return properties;
    }
    public void setProperties(Properties properties) {
        this.properties = properties;
    }

    @Override
    public String toString() {
        return "DataSource{" +
                "properties=" + properties +
                '}';
    }
}
```

### Main
```java
package com.yczlab.spring.beans.collections;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Main {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        ListPerson listPerson = (ListPerson) context.getBean("listPerson");
        System.out.println(context.getBean("car"));
        System.out.println(context.getBean("car2"));
        System.out.println(listPerson);

        System.out.println("*************************");
        MapPerson mapPerson = (MapPerson) context.getBean("mapPerson");
        System.out.println(mapPerson);

        System.out.println("*************************");
        DataSource dataSource = context.getBean(DataSource.class);
        System.out.println(dataSource.getProperties());
    }
}
```

### applicationContext.xml配置文件
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--通过构造方法来配置bean的属性-->
    <bean id="car" class="com.yczlab.spring.beans.Car">
        <constructor-arg value="Audi" index="0"></constructor-arg>
        <constructor-arg value="ShangHai" index="1"></constructor-arg>
        <constructor-arg value="300000" index="2" type="double"></constructor-arg>
    </bean>
    <!--使用构造器注入属性值可以指定参数的位置和参数的类型！以区分重载的构造器-->
    <bean id="car2" class="com.yczlab.spring.beans.Car">
        <constructor-arg value="Baoma" type="java.lang.String"></constructor-arg>
        <!--属性值也可以使用value子节点进行配置-->
        <constructor-arg type="java.lang.String">
            <value>ShanHai^</value>
        </constructor-arg>
        <constructor-arg type="int">
            <value>250</value>
        </constructor-arg>
    </bean>

    <!-- 测试如何配置集合属性 -->
    <!-- 配置List属性值 -->
    <bean id="listPerson" class="com.yczlab.spring.beans.collections.ListPerson">
        <property name="name" value="Mike"></property>
        <property name="age" value="27"></property>
        <property name="cars">
            <!-- 使用list节点为List类型的属性赋值-->
            <list>
                <ref bean="car"></ref>
                <ref bean="car2"/>
                <bean class="com.yczlab.spring.beans.Car">
                    <constructor-arg value="Ford"></constructor-arg>
                    <constructor-arg value="ChangAn"></constructor-arg>
                    <constructor-arg value="200000" type="double"></constructor-arg>
                </bean>
            </list>
        </property>
    </bean>

    <!-- 配置Map属性值 -->
    <bean id="mapPerson" class="com.yczlab.spring.beans.collections.MapPerson">
        <property name="name" value="Rose"></property>
        <property name="age" value="28"></property>
        <property name="cars">
            <!--使用map节点及map的entry子节点配置Map类型的成员变量-->
            <map>
                <entry key="AA" value-ref="car"></entry>
                <entry key="BB" value-ref="car"></entry>
            </map>
        </property>
    </bean>

    <!-- 配置Properties属性值 -->
    <bean id="dataSource" class="com.yczlab.spring.beans.collections.DataSource">
        <property name="properties">
            <!--使用props和prop节点来为Properties属性赋值-->
            <props>
                <prop key="user">root</prop>
                <prop key="password">1234</prop>
                <prop key="jdbcUrl">jdbc:mysql:///test</prop>
                <prop key="driverClass">com.mysql.jdbc.Driver</prop>
            </props>
        </property>
    </bean>
</beans>
```
