---
title: Spring配置Bean
author: YangChongZhi
tags: [Spring,Java]
date: 2020-03-07 17:45:58
id: 2
categories: Spring
cover: True
---
## 引言：
在Spring IOC容器中配置Bean，然后使用该Bean
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

### Person.java
```java
package com.yczlab.spring.beans;

public class Person {
    private String name;
    private int age;

    private Car car;

    public Person() {
    }

    public Person(String name, int age, Car car) {
        this.name = name;
        this.age = age;
        this.car = car;
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
                ", age=" + age +
                ", car=" + car +
                '}';
    }
}
```

### HelloWorld.java
```java
package com.yczlab.spring.beans;

public class HelloWorld {

    private String name;

    public HelloWorld() {
        System.out.println("HelloWorld's Constructor...");
    }
    public HelloWorld(String name) {
        this.name = name;
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        System.out.println("setName: " + name);
        this.name = name;
    }

    public void hello() {
        System.out.println("hello: " + name);
    }
}
```

### Main.java
```java
package com.yczlab.spring.beans;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Main {
    public static void main(String[] args) {
        /*
        HelloWorld helloWorld = new HelloWorld();
        helloWorld.setName("yczlab");
        helloWorld.hello();
        */

        //1、创建Spring的IOC容器对象
        //ApplicationContext(接口) 代表IOC
        //ClassPathXmlApplicationContext 是ApplicationContext接口的实现类，从类路径下加载配置文件夹
        ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
        //2、从IOC容器中获取Bean实例
        //利用id定位到IOC容器中的bean
        HelloWorld helloWorld = (HelloWorld)ctx.getBean("helloWorld");
        //利用类型放回IOC容器中的Bean，但要求IOC容器中必须只能有一个该类型的Bean
        //HelloWorld helloWorld = (HelloWorld) ctx.getBean(HelloWorld.class);
        //3、调用hello方法
        helloWorld.hello();

        System.out.println("************************");
        Car car = (Car) ctx.getBean("car");
        System.out.println(car);

        car = (Car) ctx.getBean("car2");
        System.out.println(car);

        System.out.println("************************");
        Person person = (Person) ctx.getBean("person");
        System.out.println(person);

        person = (Person) ctx.getBean("person2");
        System.out.println(person);

    }
}
```

### applicationContext.xml配置文件（src/路径下）
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--
        配置bean：
        class：bean的全类名，通过反射的方式在IOC容器中创建Bean，所以要求Bean中必须有无参的构造器
        id：标识容器中的bean，di是唯一的。
    -->
    <bean id="helloWorld2" class="com.yczlab.spring.beans.HelloWorld">
        <property name="name" value="yczlab"></property>
    </bean>
    <bean id="helloWorld" class="com.yczlab.spring.beans.HelloWorld">
        <property name="name" value="yczlab"></property>
    </bean>

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

    <bean id="person" class="com.yczlab.spring.beans.Person">
        <property name="name" value="Tom"></property>
        <property name="age" value="24"></property>
        <!--可以使用property的ref属性或ref建立bean之间的引用关系-->
        <!-- <property name="car" ref="car2"></property> -->
        <!--
        <property name="car">
            <ref bean="car2"></ref>
        </property>
        -->
        <!--也可以使用内部bean，内部bean不能被外部使用，只能在内部使用，所以id属性没必要-->
        <property name="car">
            <bean class="com.yczlab.spring.beans.Car">
                <constructor-arg value="Ford"></constructor-arg>
                <constructor-arg value="ChangAn"></constructor-arg>
                <constructor-arg value="200000" type="double"></constructor-arg>
            </bean>
        </property>
    </bean>
    <bean id="person2" class="com.yczlab.spring.beans.Person">
        <constructor-arg value="Jerry"></constructor-arg>
        <constructor-arg value="25"></constructor-arg>
        <!-- <constructor-arg ref="car"></constructor-arg> -->
        <!--测试null值，属性为引用数据类型时，可以为其赋值为null，意义不大，因为这类数据默认初始化值就是null-->
        <!-- <constructor-arg><null/></constructor-arg> -->
        <constructor-arg ref="car"></constructor-arg>
        <!-- 为级联属性赋值。注意：属性（car）需要先初始化后才能为级联属性赋值，否则会有异常 -->
        <property name="car.maxSpeed" value="250"></property>
    </bean>

</beans>
```