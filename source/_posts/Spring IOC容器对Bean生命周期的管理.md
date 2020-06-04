---
title: Spring IOC容器对Bean生命周期的管理
author: YangChongZhi
tags: [Spring,Java]
date: 2020-03-08 16:26:23
id: 3
categories: Spring
---
## 引言：
Spring的IOC容器对Bean生命周期的管理，包括Bean的后置处理器、初始化和销毁等
- Car.java
- MyBeanPostProcessor.java
- Main.java
- beans-cycle.xml
<!-- more -->

### Car.java
```java
package com.yczlab.spring.beans.cycle;

public class Car {

    public Car() {
        System.out.println("Car's Constructor...");
    }

    @Override
    public String toString() {
        return "Car{" +
                "brand='" + brand + '\'' +
                '}';
    }

    private String brand;

    public void setBrand(String brand) {
        System.out.println("setBrand...");
        this.brand = brand;
    }
    public void init() {
        System.out.println("init...");
    }

    public void destroy() {
        System.out.println("destroy...");
    }
}
```

### MyBeanPostProcessor.java
```java
package com.yczlab.spring.beans.cycle;

import org.springframework.beans.BeansException;
import org.springframework.beans.factory.config.BeanPostProcessor;

public class MyBeanPostProcessor implements BeanPostProcessor {

    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("postProcessBeforeInitialization: " + bean + ", " + beanName);

        if ("car".equals(beanName)) {
            //...
        }

        return bean;
    }

    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("postProcessAfterInitialization: " + bean + ", " + beanName);
        //可以偷梁换柱
        Car car = new Car();
        car.setBrand("Ford");
        return car;
    }
}
```

### Main.java
```java
package com.yczlab.spring.beans.cycle;

import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Main {
    public static void main(String[] args) {

        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("beans-cycle.xml");

        Car car = (Car) context.getBean("car");
        System.out.println(car);

        //关闭IOC容器
        context.close();
    }
}
```

### beans-cycle.xml配置文件
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--Spring IOC容器对Bean生命周期进行管理的过程：
            - 通过构造器或工厂方法创建Bean实例
            - 为Bean的属性设置值和对其他Bean的引用
            - 调用Bean的初始化方法
            - 正常使用Bean
            - 当容器关闭时，调用Bean的销毁方法
    -->
    <bean id="car" class="com.yczlab.spring.beans.cycle.Car"
          init-method="init" destroy-method="destroy">
        <property name="brand" value="Audi"/>
    </bean>

    <!-- 配置Bean后置处理器，实现更细粒度的控制
        实现BeanPostProcessor接口，并提供
        postProcessBeforeInitialization(Object bean, String beanName)：在init-method之前被调用
        postProcessAfterInitialization(Object bean, String beanName)：在destroy-method之后被调用
        以上两个方法的实现

        bean：bean实例本身
        beanName：IOC容器配置的bean的名字，即唯一表示id
        返回值：是实际上返回给用户的那个Bean，注意：可以在以上两个方法中修改返回的bean，甚至返回一个新的bean
    -->
    <!--配置bean的后置处理器：不需要配置id，IOC容器自动识别是一个BeanPostProcesser-->
    <bean class="com.yczlab.spring.beans.cycle.MyBeanPostProcessor"></bean>

    <!--配置了Bean后置处理器后，Spring IOC容器对Bean生命周期进行管理的过程：
            - 通过构造器或工厂方法创建Bean实例
            - 为Bean的属性设置值和对其他Bean的引用
            - 将Bean实例传递给Bean后置处理器的postProcessBeforeInitialization()方法
            - 调用Bean的初始化方法
            - 将将Bean实例传递给Bean后置处理器的postProcessAfterInitialization()方法
            - 正常使用Bean
            - 当容器关闭时，调用Bean的销毁方法
    -->

</beans>
```