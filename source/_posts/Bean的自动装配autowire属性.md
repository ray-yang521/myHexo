---
title: Bean的自动装配autowire属性
author: YangChongZhi
tags: [Spring,Java]
date: 2020-03-07 20:28:57
id: 6
categories: Spring
---
## 引言：
使用autowire属性，自动将IOC容器中的bean装配到当前所配置的bean的属性中
- Address.java
- Car.java
- Person.java
- Main.java
- beans-autowire.xml
<!-- more -->

### Address.java
```java
package com.yczlab.spring.beans.autowire;

public class Address {
    private String city;
    private String street;

    public String getCity() {
        return city;
    }

    public void setCity(String city) {
        this.city = city;
    }

    public String getStreet() {
        return street;
    }

    public void setStreet(String street) {
        this.street = street;
    }

    @Override
    public String toString() {
        return "Address{" +
                "city='" + city + '\'' +
                ", street='" + street + '\'' +
                '}';
    }
}
```

### Car.java
```java
package com.yczlab.spring.beans.autowire;

public class Car {
    private String brand;
    private double price;

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

### Person.java
```java
package com.yczlab.spring.beans.autowire;

public class Person {
    private String name;
    private Address address;
    private Car car;

    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public Address getAddress() {
        return address;
    }
    public void setAddress(Address address) {
        this.address = address;
    }
    public Car getCar() {
        return car;
    }
    public void setCar(Car car) {
        this.car = car;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", address=" + address +
                ", car=" + car +
                '}';
    }
}
```

### Main.java
```java
package com.yczlab.spring.beans.autowire;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Main {
    public static void main(String[] args) {

        ApplicationContext context = new ClassPathXmlApplicationContext("beans-autowire.xml");
        Person person = (Person) context.getBean("person");
        System.out.println(person);

    }
}
```

### beans-autowire.xml配置文件
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:p="http://www.springframework.org/schema/p"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="address" class="com.yczlab.spring.beans.autowire.Address"
          p:city="BeiJing" p:street="HuiLongGuan"/>

    <bean id="address2" class="com.yczlab.spring.beans.autowire.Address"
          p:city="DaLian" p:street="ZhongShan"/>

    <bean id="car" class="com.yczlab.spring.beans.autowire.Car"
          p:brand="Audi" p:price="300000"/>

    <!--
    可以使用autowire属性指定自动装配的方式。
    byName 根据bean的名字和当前bean的setter风格的属性名进行自动装配。若有匹配的，则进行自动装配；若没有匹配的，则不装配
    byType 根据bean的类型和当前bean的属性的类型进行自动装配。若IOC容器中有1个以上的类型匹配的bean，则抛异常。
    不能兼用2中自动装配方式。
    -->
    <bean id="person" class="com.yczlab.spring.beans.autowire.Person"
          p:name="Tom" autowire="byName"/>

</beans>
```

