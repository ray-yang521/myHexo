---
title: Bean的属性配置细节(p命名空间为bean的属性赋值)
author: YangChongZhi
tags: [Spring,Java]
date: 2020-03-07 20:00:40
id: 5
categories: Spring
---
## 引言：
在配置文件中使用p命名空间为所配置的bean的属性赋值
- Car.java
- ListPerson.java
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

### ListPerson.java
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

### Main.java
```java
package com.yczlab.spring.beans.collections;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Main {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        ListPerson listPerson2 = (ListPerson) context.getBean("listPerson2");
        System.out.println(listPerson2);
    }
}
```

### applicationContext.xml配置文件
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:uitl="http://www.springframework.org/schema/util"
       xmlns:p="http://www.springframework.org/schema/p"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/util https://www.springframework.org/schema/util/spring-util.xsd">

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

    <!--配置独立的集合Bean，以供多个Bean进行引用。需导入util命名空间-->
    <uitl:list id="cars">
        <ref bean="car"></ref>
        <ref bean="car2"/>
    </uitl:list>

    <!-- 通过 p 命名空间为bean的属性赋值，需要先导入 p 命名空间。相对于传统的配置方式更加的简洁。 -->
    <bean id="listPerson2" class="com.yczlab.spring.beans.collections.ListPerson"
          p:age="30" p:name="Queen" p:cars-ref="cars"></bean>
</beans>
```
