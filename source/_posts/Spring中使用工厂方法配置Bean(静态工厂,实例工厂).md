---
title: 'Spring中使用工厂方法配置Bean(静态工厂,实例工厂)'
author: YangChongZhi
tags: [Spring,Java]
date: 2020-03-08 16:36:55
id: 4
categories: Spring
---
## 引言：
通过静态工厂方法来配置bean，注意不是配置静态工厂方法实例，而是配置bean实例。
通过实例工厂方法来配置bean，先配置工厂的bean实例，再配置所需bean实例。
- Car.java
- StaticCarFactory.java
- InstanceCarFactory.java
- Main.java
- beans-factory.xml
<!-- more -->

### Car.java
```java
package com.yczlab.spring.beans.factory;

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

### StaticCarFactory.java
```java
package com.yczlab.spring.beans.factory;

import java.util.HashMap;
import java.util.Map;

/**
 * @Description: 静态工厂方法：直接调用某个类的静态方法就可以返回Bean的实例
 * @Author: ycz
 * @Date: 2020/3/5
 */
public class StaticCarFactory {

    private static Map<String, Car> cars = new HashMap<String, Car>();
    static{
        cars.put("audi", new Car("audi", 300000));
        cars.put("ford", new Car("ford",400000));
    }

    //静态工厂方法
    public static Car getCar(String name) {
        return cars.get(name);
    }
}
```

### InstanceCarFactory.java
```java
package com.yczlab.spring.beans.factory;

import java.util.HashMap;
import java.util.Map;

/**
 * @Description: 实例工厂的方法：即先需要创建工厂本身，再调用实例化后的工厂的方法返回bean实例
 * @Author: ycz
 * @Date: 2020/3/5
 */
public class InstanceCarFactory {

    private Map<String, Car> cars = null;

    public InstanceCarFactory() {
        cars = new HashMap<String, Car>();
        cars.put("audi", new Car("audi", 300000));
        cars.put("ford", new Car("ford", 400000));
    }

    public Car getCar(String brand) {
        return cars.get(brand);
    }

}
```

### Main.java
```java
package com.yczlab.spring.beans.factory;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Main {
    public static void main(String[] args) {

        ApplicationContext context = new ClassPathXmlApplicationContext("beans-factory.xml");

        Car car1 = (Car) context.getBean("staticCar");
        System.out.println(car1);

        System.out.println("**********************");
        Car car2 = (Car) context.getBean("instanceCar");
        System.out.println(car2);

    }
}
```

### beans-factory.xml配置文件
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--通过静态工厂方法来配置bean，注意不是配置静态工厂方法实例，而是配置bean实例-->
    <!--
        class属性：指向静态工厂方法的全类名
        factory-method：指向静态工厂方法的名字
        constructor-arg：如果工厂方法需要传入参数，则使用constructor-arg来配置参数
    -->
    <bean id="staticCar" class="com.yczlab.spring.beans.factory.StaticCarFactory"
          factory-method="getCar">
        <constructor-arg value="audi"></constructor-arg>
    </bean>


    <!--配置工厂的实例-->
    <bean id="carFactory" class="com.yczlab.spring.beans.factory.InstanceCarFactory"/>
    <!--通过实例工厂的方法来配置bean-->
    <!--
        class属性：不需要
        factory-bean：指向工厂的实例bean
        constructor-arg：如果工厂方法需要传入参数，则使用constructor-arg来配置参数
    -->
    <bean id="instanceCar" factory-bean="carFactory" factory-method="getCar">
        <constructor-arg value="ford"/>
    </bean>

</beans>
```