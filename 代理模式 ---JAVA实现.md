## 代理模式 ---JAVA实现

### 一 代理模式

​			![img](https://img-blog.csdn.net/20180918092105430)

​         **就如同我们身边的中介一样，比如租房中介，他并没有房子，但是可以向我们租房子，但是都知道他只是代替了房东向我们租房子的工作，我们可以称中介为代理。代理模式也是一样的道理，自己的类并不去自己实现自己的工作，有代理类去帮忙实现。**

### 二 静态代理

​	   **静态代理 代理类通过实现被代理类相同的接口，提供一个代理类实现接口的带参构造器，通过构造器在初始化对象时注入，实现方法中去调用被代理类的实现方法。在这个时候我们一个类为其编写一个真是存在的代理类，这就叫做静态代理，代码清单如下：**

 **同时实现的接口：Subject.java**

```java
public interface Subject{
    void subject(String param);
}
```

**被代理类：MySubject.java**

```java
public class MySubject implements Subject{
    @Override
    public void subject(String param) {
        System.out.println(param);;
    }
}
```

**代理类：StaticProxy.java**

```java
public class StaticProxy implements Subject {
    Subject subject;

    public StaticProxy(Subject subject) {
        this.subject = subject;
    }
    @Override
    public void subject(String param) {
        System.out.println("start");
        subject.subject(param);
        System.out.println("end");
    }
     public static void main(String[] args) {
        Subject subject = new MySubject();
        StaticProxy staticProxy = new StaticProxy(subject);
        staticProxy.subject("helloword");
    }
}
```

**我们运行StaticProxy.java 中的接口便可以看到执行结果如下:**

```java
start
helloword
end

Process finished with exit code 0
```

**仔细观察静态代理的实现方式，存在一个缺点，就是如果被代理类有很多需要被代理的方法，那么我们需要在代理类中重写很多的方法，多个类需要被代理，我们需要写多个代理类，有没有一种简化这种操作的方法呢？**

### 三 动态代理

**在java中动态代理主要分为两种：JDK动态代理，CGLIB动态代理，下面有相关的使用和底层实现原理**



#### 1.JDK动态代理

**跟JDK动态代理的实现相关的类位于反射包下的Invocation Handler接口，Proxy类**

**InvocationHandler.java源码如下**

```java
package java.lang.reflect;

public interface InvocationHandler {
    public Object invoke(Object proxy, Method method, Object[] args)
        throws Throwable;
}
```

**在Invocation Handler接口中只存在一个接口，我们只需要让我们的代理类实现这个接口，并利用Method类中的invoke方法执行method参数方法即可，但是需要被代理类的实例和args**

**Proxy.java中先需要看newProxyInstance方法：**

```java

public static Object newProxyInstance(ClassLoader loader,
                                          Class<?>[] interfaces,
                                          InvocationHandler h)
        throws IllegalArgumentException
    {
        
    }

```

**这个接口中有三个参数，第一个参数loader，为我们代理类的ClassLoader，interfaces为被代理类实现的或有接口，h为代理类的实例，下面我们先实现一个JDK动态代理**



**一个父接口：SubInter.java**

```java
public interface SubInter {
    void  hello(String param);
    void fly(String param);
}
```

**父接口的实现类：SubImpl**

```java
public class SubImpl implements  SubInter{

    @Override
    public void hello(String parem) {
        System.out.println(parem);
    }
    @Override
    public void fly(String param) {
        System.out.println("I can fly");
    }
}
```

**代理类：MyProxy.java**

```java
public class MyProxy implements InvocationHandler{
    SubInter subInter;

    public MyProxy(SubInter subInter) {
        this.subInter = subInter;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("begin");
        Object o = method.invoke(subInter,args);
        System.out.println("end");
        return o;
    }
    public static void main(String[] args) {
        SubInter subInter = new SubImpl();
        MyProxy myProxy = new MyProxy(subInter);
        SubInter subInter1 = (SubInter) Proxy.newProxyInstance(myProxy.getClass().getClassLoader(),subInter.getClass().getInterfaces(),myProxy);
        subInter1.hello("hello world");
        subInter1.fly("didid");
    }
}

```

**运行MyProxy.java中的main方法，得到以下的运行结果**

```java
begin
hello world
end
begin
I can fly
end
```

**JDK动态代理底层是如何实现的呢？**

