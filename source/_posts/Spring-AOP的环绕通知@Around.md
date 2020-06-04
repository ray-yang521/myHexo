---
title: Spring AOP的环绕通知@Around
author: YangChongZhi
tags: [Spring,Java]
date: 2020-03-11 21:08:52
id: 4
categories: Spring
---
## 引言：
Spring AOP可以使用@Around注解来实现环绕通知，环绕通知需要携带ProceedingJoinPoint类型的参数，环绕通知类似于动态代理的全过程，ProceedingJoinPoint类型的参数可以决定是否执行目标方法，且环绕通知必须要有返回值，返回值即为目标方法的返回值。

- ArithmeticCalculator.java
- ArithmeticCalculatorImpl.java
- LoggingAspect.java
- Main.java
- applicationContext.xml

<!-- more -->
### ArithmeticCalculator.java
```java
package com.yczlab.spring.aop.impl;

public interface ArithmeticCalculator {
    int add(int i, int j);
    int sub(int i, int j);
    int mul(int i, int j);
    int div(int i, int j);
}
```

### ArithmeticCalculatorImpl.java
```java
package com.yczlab.spring.aop.impl;

import org.springframework.stereotype.Component;

@Component
public class ArithmeticCalculatorImpl implements ArithmeticCalculator {
    @Override
    public int add(int i, int j) {
        int result = i + j;
        return result;
    }

    @Override
    public int sub(int i, int j) {
        int result = i - j;
        return result;
    }

    @Override
    public int mul(int i, int j) {
        int result = i * j;
        return result;
    }

    @Override
    public int div(int i, int j) {
        int result = i / j;
        return result;
    }
}
```

### LoggingAspect.java
```java
package com.yczlab.spring.aop.impl;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.*;
import org.springframework.stereotype.Component;

import java.util.Arrays;
import java.util.List;

//把这个类声明为一个切面：需要把该类放入到IOC容器中，再声明为一个切面
@Aspect //声明为一个切面，使用该注解需要“aspectjweaver-xxx.jar”包的支持，"xxx"代表版本
@Component //放入IOC容器中
public class LoggingAspect {

    //声明该方法是一个前置通知：在目标方法开始之前执行
    //如果这样写execution(public int com.yczlab.spring.aop.impl.ArithmeticCalculator.add(int, int))
    //那么只对ArithmeticCalculator.add方法起作用
    @Before("execution(public int com.yczlab.spring.aop.impl.ArithmeticCalculator.*(int, int))")
    public void beforeMethod(JoinPoint joinPoint) {
        //可以通过JoinPoint参数来访问到连接的细节，如方法名称和参数值等
        String methodName = joinPoint.getSignature().getName();
        List<Object> args = Arrays.asList(joinPoint.getArgs());
        System.out.println("The method " + methodName + " begins with " + args);
    }

    //声明该方法是一个后置通知：在目标方法执行后（无论是否发生异常），再执行的通知
    //但是在后置通知中还不能访问目标方法的执行结果
    @After("execution(* com.yczlab.spring.aop.impl.ArithmeticCalculator.*(int, int))")
    public void afterMethod(JoinPoint joinPoint) {
        String methodName = joinPoint.getSignature().getName();
        System.out.println("The method "+methodName+" ends");
    }

    //声明该方法是一个返回通知：在目标方法正常结束时（出异常时就不执行该通知），执行的通知
    //在返回通知中可以访问目标方法的返回值，即执行结果
    @AfterReturning(value = "execution(* com.yczlab.spring.aop.impl.ArithmeticCalculator.*(..))", returning = "result")
    public void AfterReturning(JoinPoint joinPoint, Object result) {
        String methodName = joinPoint.getSignature().getName();
        System.out.println("The method " + methodName + " ends with " + result);
    }

    //声明该方法是一个异常通知：在目标方法出现异常时执行的通知
    //在异常通知中可以访问到异常对象，且可以指定在出现特定异常时才执行通知代码
    @AfterThrowing(value = "execution(* com.yczlab.spring.aop.impl.ArithmeticCalculator.*(..))", throwing = "ex")
    public void AfterThrowing(JoinPoint joinPoint, Exception ex) {
        String methodName = joinPoint.getSignature().getName();
        System.out.println("The method " + methodName + " occurs exception: " + ex);
    }

    //声明该方法是一个环绕通知：
    /*
    * 环绕通知需要携带ProceedingJoinPoint类型的参数
    * 环绕通知类似于动态代理的全过程：ProceedingJoinPoint类型的参数可以决定是否执行目标方法
    * 且环绕通知必须要有返回值，返回值即为目标方法的返回值
    * */
    @Around("execution(* com.yczlab.spring.aop.impl.ArithmeticCalculator.*(..))")
    public Object aroundMethod(ProceedingJoinPoint pjd) {
        Object result = null;
        String methodName = pjd.getSignature().getName();
        List<Object> args = Arrays.asList(pjd.getArgs());

        //执行目标方法
        try {
            //前置通知，注意与上面@Before注解配置的前置通知相区分
            System.out.println("环绕通知中的前置通知：The method " + methodName + " begins with " + args);
            result = pjd.proceed();
            //返回通知，注意与上面@AfterReturning注解配置的返回通知相区分
            System.out.println("环绕通知中的返回通知：The method " + methodName + " ends with " + result);
        } catch (Throwable e) {
            //异常通知，注意与上面@AfterThrowing注解配置的异常通知相区分
            System.out.println("环绕通知中的异常通知：The method " + methodName + " occurs exception: " + e);
        }
        //后置通知，注意与上面@After注解配置的后置通知相区分
        System.out.println("环绕通知中的后置通知：The method "+methodName+" ends");

        //如果以上情况确有异常发生，返回的值result=null, 无法装换为int类型，还会发生其他异常，不要惊慌
        return result;
    }
}
```

### Main.java
```java
package com.yczlab.spring.aop.impl;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Main {
    public static void main(String[] args) {

        //1、创建Spring的IOC容器
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");

        //2、从IOC容器中获取bean的实例
        ArithmeticCalculator arithmeticCalculator = context.getBean(ArithmeticCalculator.class);

        //3、使用bean
        //测试环绕通知（环绕通知类似于动态代理）
        System.out.println("****************");
        int result = arithmeticCalculator.sub(12, 6);
        System.out.println("result: " + result);
        System.out.println("****************");
        result = arithmeticCalculator.mul(3, 4);
        System.out.println("result: " + result);
        System.out.println("****************");
        //测试异常情况，“除零”异常发生时返回值为null，无法转换为int类型，会在次发生异常。主要测试“除零”异常
        result = arithmeticCalculator.div(100, 0);//测试异常情况，异常发生时返回值为null，无法转换为int
        System.out.println("result: " + result);
    }
}
```

### applicationContext.xml配置文件
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop.xsd">

    <!--配置自动扫描的包-->
    <context:component-scan base-package="com.yczlab.spring.aop.impl"/>

    <!--使AspectJ注解起作用：自动为匹配的类生成代理对象-->
    <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
</beans>
```

