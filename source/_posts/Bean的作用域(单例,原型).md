---
title: 'Bean的作用域(单例,原型)'
author: YangChongZhi
tags: [Spring,Java]
date: 2020-03-07 21:05:26
id: 8
categories: Spring
---
## 引言：
使用bean的scope属性来配置bean的作用域
- Car.java
- Main.java
- beans-scope.xml
<!-- more -->

### Car.java
```java
package com.yczlab.spring.beans.scope;

public class Car {
    private String brand;
    private double price;

    public Car() {
        System.out.println("Car's Constructor...");
    }
    public Car(String brand, double price) {
        this.brand = brand;
        this.price = price;
    }

    public String getBrand() {
        return brand;
    }
    public void setBrand(String brand) {
        this.brand = brand;
    }
    public double getPrice() {
        return price;
    }
    public void setPrice(double price) {
        this.price = price;
    }

    @Override
    public String toString() {
        return "Car{" +
                "brand='" + brand + '\'' +
                ", price=" + price +
                '}';
    }
}
```

### Main.java
```java
package com.yczlab.spring.beans.scope;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Main {
    public static void main(String[] args) {

        ApplicationContext context = new ClassPathXmlApplicationContext("beans-scope.xml");

        Car car = (Car) context.getBean("car");
        Car car2 = (Car) context.getBean("car");
        System.out.println(car);
        System.out.println(car2);
        System.out.println(car == car2);
    }
}
```

### beans-scope.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--
        使用bean的scope属性来配置bean的作用域
        singleton：默认值。容器初始化时创建Bean实例，在整个容器的生命周期内只创建这一个bean，单例的。
        prototype：原型的。容器初始化时不创建Bean的实例，而在每次请求时都创建一个新的Bean实例，并返回。
    -->
    <bean id="car" class="com.yczlab.spring.beans.scope.Car" scope="prototype">
        <property name="brand" value="Audi"/>
        <property name="price" value="300000"></property>
    </bean>

</beans>
```