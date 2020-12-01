								# Spring aop

## 一、aop是什么

 

```c++
  AOP（Aspect Oriented Programming）称为面向切面编程，在程序开发中主要用来解决一些系统层面上的问题，比如日志，事务，权限等待，Struts2的拦截器设计就是基于AOP的思想，是个比较经典的例子。

  在不改变原有的逻辑的基础上，增加一些额外的功能。代理也是这个功能，读写分离也能用aop来做。
```

```
·横切关注点：对哪些方法进行拦截，拦截后怎么处理，这些关注点称之为横切关注点
·切面（Aspect）:通常为一个类，里面可以定义切入点和通知
·JoinPoint(连接点):程序执行过程中明确的点，所有Spring表示被拦截到的方法，实际上连接点还可以是字段或者构造器
·Advice(通知):AOP在特定的切入点上执行的增强处理，有before(前置),after(后置),afterReturning(最终),afterThrowing(异常),around(环绕)
·Pointcut(切入点)：就是带有通知的连接点，在程序中主要体现为书写切入点表达式
·织入：将切面应用到目标对象并导致代理对象创建的过程
```

```
应用场景：
Authentication 权限
Caching 缓存
Context passing 内容传递
Error handling 错误处理
Lazy loading　懒加载
Debugging　　调试
logging, tracing, profiling and monitoring　记录跟踪　优化　校准
Performance optimization　性能优化
Persistence　　持久化
Resource pooling　资源池
Synchronization　同步
Transactions 事务
```

## 二.导入jar包 pom.xml

```xml
 <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>3.8.1</version>
            <scope>test</scope>
        </dependency>
        <!-- Spring -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.0.8.RELEASE</version>
        </dependency>

        <!--spring aop + aspectj-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aop</artifactId>
            <version>5.0.8.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjrt</artifactId>
            <version>1.8.9</version>
        </dependency>
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.8.9</version>
 </dependency>
```

## 三.aop的实现

#### 1.经典的基于代理的AOP

1.HelloWord.java

```java
public interface HelloWord {
    void printHelloWord();
    void doPrint();
}
```

2.HelloWord1.java

```java
public class HelloWord1 implements HelloWord {

    public void printHelloWord() {
        System.out.println("1111111111111111+ helloword");
    }

    public void doPrint() {
        System.out.println("do hellowor 1111111111");
    }
}

```

3.HelloWord2.java

```java
public class HelloWord2 implements  HelloWord{
    public void printHelloWord() {
        System.out.println("22222222222222222222+ helloword");
    }

    public void doPrint() {
        System.out.println("doprint2");
    }
}

```

4.TimerHandle

```java
import org.springframework.aop.AfterReturningAdvice;
import org.springframework.aop.MethodBeforeAdvice;

import java.lang.reflect.Method;

public class TimerHandle implements MethodBeforeAdvice, AfterReturningAdvice {


    public void afterReturning(Object o, Method method, Object[] objects, Object o1) throws Throwable {
        System.out.println("代理后current" + System.currentTimeMillis());
    }

    public void before(Method method, Object[] objects, Object o) throws Throwable {
        System.out.println("代理前current" + System.currentTimeMillis());
    }
}
```

5.spring.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
    <bean id="helloWord1" class="aop.HelloWord1"></bean>
    <bean id="helloWord2" class="aop.HelloWord2"></bean>
    <bean id="timeHandler" class="aop.TimerHandle"></bean>
    <bean id="timePoint" class="org.springframework.aop.support.JdkRegexpMethodPointcut">
        <property name="pattern" value=".*doPrint"></property>
    </bean>
    <bean id="timeHandlerAdvisor" class="org.springframework.aop.support.DefaultPointcutAdvisor">
        <property name="advice" ref="timeHandler"></property>
        <property name="pointcut" ref="timePoint"></property>
    </bean>
    <bean id="proxy" class="org.springframework.aop.framework.ProxyFactoryBean">
        <!-- 代理的对象，有打印时间能力 -->
        <property name="target" ref="helloWord1"></property>
        <!-- 使用切面 -->
        <property name="interceptorNames" value="timeHandlerAdvisor"></property>
        <!-- 代理接口，hw接口 -->
        <property name="proxyInterfaces" value="aop.HelloWord"></property>
    </bean>
    <!-- 设置代理 -->
    <bean id="proxy2" class="org.springframework.aop.framework.ProxyFactoryBean">
        <!-- 代理的对象，有打印时间能力 -->
        <property name="target" ref="helloWord2"></property>
        <!-- 使用切面 -->
        <property name="interceptorNames" value="timeHandlerAdvisor"></property>
        <!-- 代理接口，hw接口 -->
        <property name="proxyInterfaces" value="aop.HelloWord"></property>
    </bean>

</beans>
```

6.代码分析

```
1.切面：为TimerHandle类,其实现了用于aop的MethodBeforeAdvice, AfterReturningAdvice接口，实现方法，玩成方法执行前和执行后逻辑的定义
2.连接点：所有的doPrint方法
3.
```

