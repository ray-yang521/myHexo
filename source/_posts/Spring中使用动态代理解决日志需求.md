---
title: Spring中使用动态代理解决日志需求
author: YangChongZhi
tags: [Spring,Java]
date: 2020-03-09 19:05:58
id: 1
categories: Spring
---
## 引言：
使用动态代理技术实现在类的方法中加入日志信息。通过动态代理的方式写入日志信息不会给原来的类带来代码混乱和分散的问题，便于维护
- ArithmeticCalculator.java
- ArithmeticCalculatorImpl.java
- ArithmeticCalculatorLoggingImpl.java
- ArithmeticCalculatorLoggingProxy.java
- Main.java
<!-- more -->

### ArithmeticCalculator.java
```java
package com.yczlab.spring.aop.helloworld;

public interface ArithmeticCalculator {
    int add(int i, int j);
    int sub(int i, int j);
    int mul(int i, int j);
    int div(int i, int j);
}
```

### ArithmeticCalculatorImpl.java
```java
package com.yczlab.spring.aop.helloworld;

//该实现类的方法中不带日志信息，而通过动态代理的方式写入日志信息，便于维护
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

### ArithmeticCalculatorLoggingImpl.java
```java
package com.yczlab.spring.aop.helloworld;

//直接在该实现类的方法中写入日志信息
public class ArithmeticCalculatorLoggingImpl implements ArithmeticCalculator {
    @Override
    public int add(int i, int j) {
        System.out.println("The method add() begins with[" + i + "," + j + "]");
        int result = i + j;
        System.out.println("The method add() ends with " + result);
        return result;
    }

    @Override
    public int sub(int i, int j) {
        System.out.println("The method sub() begins with[" + i + "," + j + "]");
        int result = i - j;
        System.out.println("The method sub() ends with " + result);
        return result;
    }

    @Override
    public int mul(int i, int j) {
        System.out.println("The method mul() begins with[" + i + "," + j + "]");
        int result = i * j;
        System.out.println("The method mul() ends with " + result);
        return result;
    }

    @Override
    public int div(int i, int j) {
        System.out.println("The method div() begins with[" + i + "," + j + "]");
        int result = i / j;
        System.out.println("The method div() ends with " + result);
        return result;
    }
}
```

### ArithmeticCalculatorLoggingProxy.java
```java
package com.yczlab.spring.aop.helloworld;

import org.springframework.cglib.proxy.InvocationHandler;
import org.springframework.cglib.proxy.Proxy;

import java.lang.reflect.Method;
import java.util.Arrays;

//使用该动态代理类，解决ArithmeticCalculatorImpl类方法中的日志问题
public class ArithmeticCalculatorLoggingProxy {

    //要代理的对象
    private ArithmeticCalculator target;

    public ArithmeticCalculatorLoggingProxy(ArithmeticCalculator target) {
        this.target = target;
    }

    public ArithmeticCalculator getLoggingProxy() {
        ArithmeticCalculator proxy = null;

        //代理对象有哪一个类加载器负责加载
        ClassLoader loader = target.getClass().getClassLoader();
        //代理对象的类型，即其中有哪些方法。可以通过ArithmeticCalculator.class.getMethods()返回一个方法数组Method[]
        Class[] interfaces = new Class[]{ArithmeticCalculator.class};
        //当调用代理对象其中的方法时，该执行的代码
        InvocationHandler h=new InvocationHandler() {
            /*
            * proxy：正在返回的那个代理对象，一般情况下，invoke方法中都不使用该对象。
            * method：正在被调用的方法
            * args：调用方法时，传入的参数
            * */
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                //在这里使用proxy时会出现死循环，StackOverflowError。因为一旦使用，又会被动态代理转移到这儿调用invoke，出现死循环
                //System.out.println(proxy.toString());

                String methodName = method.getName();//获取方法名
                //日志
                System.out.println("<-yczlab-> The method " + methodName + " begins with " + Arrays.asList(args));
                //执行方法
                Object result = method.invoke(target, args);
                //日志
                System.out.println("<-yczlab-> The method " + methodName + "ends with " + result);
                return result;
            }
        };
        proxy = (ArithmeticCalculator) Proxy.newProxyInstance(loader, interfaces, h);

        return proxy;
    }

}
```

### Main.java
```java
package com.yczlab.spring.aop.helloworld;

public class Main {
    public static void main(String[] args) {
        //直接在实现类ArithmeticCalculatorLoggingImpl的方法中写日志信息
        ArithmeticCalculator arithmeticCalculatorLogging;
        arithmeticCalculatorLogging = new ArithmeticCalculatorLoggingImpl();
        int result = arithmeticCalculatorLogging.add(1, 2);
        System.out.println("-->" + result);
        result = arithmeticCalculatorLogging.div(4, 2);
        System.out.println("-->" + result);

        System.out.println("***********************************");
        //通过使用动态代理的方式写入日志信息
        ArithmeticCalculator target = new ArithmeticCalculatorImpl();
        ArithmeticCalculator proxy = new ArithmeticCalculatorLoggingProxy(target).getLoggingProxy();
        result = proxy.add(1, 2);
        System.out.println("-->" + result);
        result = proxy.div(4, 2);
        System.out.println("-->" + result);

    }
}
```

