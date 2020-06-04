---
title: Bean之间的继承依赖关系
author: YangChongZhi
tags: [Spring,Java]
date: 2020-03-07 20:43:00
id: 7
categories: Spring
---
## 引言：
bean的配置可以被继承，“子类bean”使用parent属性指定继承哪个“父类bean”的配置
- Address.java
- Car.java
- Person.java
- Main.java
- beans-relation.xml
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
package com.yczlab.spring.beans.relation;

import com.yczlab.spring.beans.autowire.Address;
import com.yczlab.spring.beans.autowire.Person;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Main {
    public static void main(String[] args) {

        ApplicationContext context = new ClassPathXmlApplicationContext("beans-relation.xml");
        //抽象Bean在IOC容器中不会被实例化
        //Address address = (Address) context.getBean("address");
        //System.out.println(address);

        Address address = (Address) context.getBean("address2");
        System.out.println(address);

        System.out.println("******************************");
        address = (Address) context.getBean("address3");
        System.out.println(address);

        System.out.println("******************************");
        address = (Address) context.getBean("address4");
        System.out.println(address);

        System.out.println("******************************");
        Person person = (Person) context.getBean("person");
        System.out.println(person);

    }
}
```

### beans-relation.xml配置文件
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:p="http://www.springframework.org/schema/p"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--
    抽象bean：bean的abstract属性为true的bean，这样的bean不能被IOC容器实例化，只用来继承配置，但是abstract属性不会被继承
    若某一个bean的class属性没有指定，则该bean必须是一个抽象bean
    -->
    <bean id="address" class="com.yczlab.spring.beans.autowire.Address"
          p:city="BeiJing^" p:street="WuDaoKou" abstract="true"/>

    <bean id="address2" class="com.yczlab.spring.beans.autowire.Address"
          p:city="BeiJing" p:street="DaZhongSi"/>

    <!--bean配置的继承：使用bean的parent属性指定继承那个bean的配置，但是abstract属性并不会被继承-->
    <bean id="address3" class="com.yczlab.spring.beans.autowire.Address"
          parent="address"/>

    <bean id="address4" class="com.yczlab.spring.beans.autowire.Address"
          parent="address2" p:street="DaZhongSi"/>


    <bean id="car" class="com.yczlab.spring.beans.autowire.Car"
          p:brand="Audi" p:price="300000"/>
    <!--
    depends-on属性：要求在配置Person时，必须有一个关联的car。换句话说person这个bean依赖于Car这个bean,但是并不会装配这个car
    depends-on属性设置Bean前置依赖的Bean，前置依赖度Bean会在本Bean实例化之前创建好，如果依赖多个Bean，则通过逗号或空格隔开
    -->
    <bean id="person" class="com.yczlab.spring.beans.autowire.Person"
          p:name="Tom" p:address-ref="address2" depends-on="car"/>

</beans>
```
