---
title: Spring通过FactoryBean来配置bean
author: YangChongZhi
tags: [Spring,Java]
date: 2020-03-08 16:49:58
id: 5
categories: Spring
---
## 引言：
通过实现FactoryBean接口来配置bean
- Car.java
- CarFactoryBean.java
- Main.java
- beans-beanfactory.xml
<!-- more -->

### Car.java
```java
package com.yczlab.spring.beans.factorybean;

public class Car {
    private String brand;
    private double price;

    public Car() {
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

### CarFactoryBean.java
```java
package com.yczlab.spring.beans.factorybean;

import org.springframework.beans.factory.FactoryBean;

//自定义的FactoryBean需要实现FactoryBean接口
public class CarFactoryBean implements FactoryBean<Car> {

    private String brand;

    public void setBrand(String brand) {
        this.brand = brand;
    }

    //返回bean的对象
    @Override
    public Car getObject() throws Exception {
        return new Car(brand, 500000);
    }

    //返回bean的类型
    @Override
    public Class<?> getObjectType() {
        return Car.class;
    }

    //是否为单实例
    @Override
    public boolean isSingleton() {
        return true;
    }
}
```

### Main.java
```java
package com.yczlab.spring.beans.factorybean;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Main {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("beans-beanfactory.xml");

        Car car = (Car) context.getBean("car");
        System.out.println(car);

    }
}
```

### beans-beanfactory.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--
        通过FactoryBean来配置Bean的实例
        class：指向FactoryBean的全类名
        property：配置FactoryBean的属性
        但实际返回的实例却是FactoryBean的getObject()方法返回的实例！
    -->
    <bean id="car" class="com.yczlab.spring.beans.factorybean.CarFactoryBean">
        <property name="brand" value="BMW"/>
    </bean>

</beans>
```