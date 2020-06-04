---
title: Spring中使用SpEL为Bean的属性动态赋值
author: YangChongZhi
tags: [Spring,Java]
date: 2020-03-08 15:34:54
id: 2
categories: Spring
---
## 引言：
可以在Spring中使用SpEL语言为Bean的属性赋一个字面值，引用其他类的静态属性，引用其他的Bean，引用其他Bean的属性，还可以在SpEL中使用运算符。
- Address.java
- Car.java
- Person.java
- Main.java
- beans-spel.xml
<!-- more -->

### Address.java###
```java
package com.yczlab.spring.beans.spel;

public class Address {
    private String city;
    private String street;

    public Address() {
    }

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
package com.yczlab.spring.beans.spel;

public class Car {
    private String brand;
    private double price;
    //轮胎周长
    private double tyrePerimeter;

    public Car() {
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
    public double getTyrePerimeter() {
        return tyrePerimeter;
    }
    public void setTyrePerimeter(double tyrePerimeter) {
        this.tyrePerimeter = tyrePerimeter;
    }

    @Override
    public String toString() {
        return "Car{" +
                "brand='" + brand + '\'' +
                ", price=" + price +
                ", tyrePerimeter=" + tyrePerimeter +
                '}';
    }
}
```

### Person.java
```java
package com.yczlab.spring.beans.spel;

public class Person {
    private String name;
    private Car car;
    private String city;//打算 引用address bean的city属性
    private String info;//打算 根据car的price属性确定info。price>=300000,为金领；否则为白领

    public Person() {
    }

    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public Car getCar() {
        return car;
    }
    public void setCar(Car car) {
        this.car = car;
    }
    public String getCity() {
        return city;
    }
    public void setCity(String city) {
        this.city = city;
    }
    public String getInfo() {
        return info;
    }
    public void setInfo(String info) {
        this.info = info;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", car=" + car +
                ", city='" + city + '\'' +
                ", info='" + info + '\'' +
                '}';
    }
}
```

### Main.java
```java
package com.yczlab.spring.beans.spel;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Main {
    public static void main(String[] args) {

        ApplicationContext context = new ClassPathXmlApplicationContext("beans-spel.xml");

        Address address = (Address) context.getBean("address");
        System.out.println(address);

        System.out.println("****************");
        Car car = (Car) context.getBean("car");
        System.out.println(car);

        System.out.println("****************");
        Person person = (Person) context.getBean("person");
        System.out.println(person);

    }
}
```

### beans-spel.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="address" class="com.yczlab.spring.beans.spel.Address">
        <!--使用SpEL为属性赋一个字面值-->
        <property name="city" value="#{'BeiJing'}"/>
        <property name="street" value="WuDaoKou"/>
    </bean>

    <bean id="car" class="com.yczlab.spring.beans.spel.Car">
        <property name="brand" value="Audi"/>
        <property name="price" value="500000"/>
        <!--使用SpEL引用类的静态属性-->
        <property name="tyrePerimeter" value="#{T(java.lang.Math).PI * 80}"/>
    </bean>

    <bean id="person" class="com.yczlab.spring.beans.spel.Person">
        <!--使用SpEL来引用其他的Bean-->
        <property name="car" value="#{car}"/>
        <!--使用SpEL来引用其他Bean的属性-->
        <property name="city" value="#{address.city}"/>
        <!-- 在SpEL中使用运算符 -->
        <property name="info" value="#{car.price > 300000 ? '金领':'白领'}"/>
        <property name="name" value="Tom"/>
    </bean>

</beans>
```
