---
title: 基于AspectJ注解来使用Spring AOP
author: YangChongZhi
tags: [Spring,Java]
date: 2020-03-09 19:59:06
id: 3
categories: Spring
---
## 引言：
AspectJ：Java社区里最完整最流行的AOP框架.在Spring2.0以上版本中, 可以使用基于AspectJ注解或基于XML配置的AOP
<!-- more -->

#### 1、在Spring中启用AspectJ注解支持
- 要在Spring应用中使用AspectJ注解,必须在classpath下包含AspectJ类库: [aopalliance.jar](aopalliance-1.0.jar)、[aspectjweaver.jar](aspectjweaver-1.9.4.jar)和[spring-aspects.jar](spring-aspects-5.2.3.jar)
- 要在Spring IOC容器中启用AspectJ注解支持,只要在Bean配置文件中定义一个空的XML元素```<aop:aspectj-autoproxy>```
- 当Spring IOC容器侦测到Bean配置文件中的```<aop:aspectj-autoproxy>```元素时,会自动为与AspectJ切面匹配的Bean创建代理.

#### 2、用AspectJ注解声明切面
- 切面首先是一个IOC中的bean,即加入@Component注解
- 要在Spring中声明AspectJ切面, 还需要加入@Aspect注解
当在Spring IOC容器中初始化AspectJ切面之后,Spring IOC容器就会为那些与AspectJ切面相匹配的Bean创建代理.
- 在AspectJ注解中,切面只是一个带有@Aspect注解的Java类.
- 通知是切面中标注有某种注解的简单的Java方法.

#### 3、AspectJ支持5种类型的通知注解:
- @Before: 前置通知, 在方法执行之前执行
- @After: 后置通知, 在方法执行之后执行
- @AfterReturning: 返回通知, 在方法返回结果之后执行
- @AfterThrowing: 异常通知, 在方法抛出异常之后
- @Around: 环绕通知, 围绕着方法执行

#### 4、编写AspectJ切入点表达式
- execution * com.yczlab.spring.aop.impl.ArithmeticCalculator.*(..)
匹配ArithmeticCalculator中声明的所有方法,第一个“*”代表任意修饰符以及任意返回值；第二个“*”代表任意方法；“..”匹配任意数量的参数. 若目标类、接口与该切面在同一个包中, 可以省略包名.
- execution public * ArithmeticCalculator.*(..)
匹配ArithmeticCalculator接口的所有公有方法.
- execution public double ArithmeticCalculator.*(..)
匹配ArithmeticCalculator中返回double类型数值的方法
- execution public double ArithmeticCalculator.*(double, ..)
匹配第一个参数为double类型的方法, “..”匹配任意数量任意类型的参数
- execution public double ArithmeticCalculator.*(double, double)
匹配参数类型为double,double类型的方法.

#### 5、可以在通知方法中声明一个类型为JoinPoint的参数. 然后就能访问链接细节. 如方法名称和参数值.
