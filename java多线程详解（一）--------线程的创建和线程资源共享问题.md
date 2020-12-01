# java多线程详解（一）--------线程的生命周期和资源共享问题

## 1.线程和进程

- 进程：一般情况下一个软件就是一个进程，为提供服务的最小单位，例如聊天使用的QQ，微信，运行java代码的虚拟机，用的服务器tomcat，都是一个进程
- 线程：一个进程中可以有多个线程，比如QQ跟多个人聊天，tomcat同时处理多个请求（tomcat天然支持多线程，可以使开发人员轻松写出并发的服务）

## 2.java中线程的创建

- 继承Thread类，重写run方法

  ```java
  public class MyThread  extends  Thread{
      private String name;
      public MyThread(String name){
          this.name = name;
      }
      @Override
      public void run(){
          for(int i=0;i<3;i++){
              System.out.println("当前进程" + this.name + " is in the " +  i + " times loop");
          }
      }
  
      public static void main(String[] args) {
          Thread thread1 = new MyThread("thread1");
          Thread thread2 = new MyThread("thread2");
          thread1.start();
          thread2.start();
      }
  }
  ```

  程序运行打印如下：	

```
当前进程thread1 is in the 0 times loop
当前进程thread2 is in the 0 times loop
当前进程thread2 is in the 1 times loop
当前进程thread2 is in the 2 times loop
当前进程thread1 is in the 1 times loop
当前进程thread1 is in the 2 times loop
```

按照程序一般的运行逻辑，应该是thread1循环执行完毕，再会执行thread2的循环，但是结果显然，thread1和thread2循环交替执行，说明两个循环并发执行，便实现了多线程。（一个疑问：这个进程中有多少个线程？）



- 实现Runnable 接口，实现run方法

  ```java
  public class RunThread implements Runnable {
      private String name;
      public RunThread(String name){
          this.name = name;
      }
      @Override
      public void run() {
          for(int i=0;i<3;i++){
              System.out.println("当前进程" + this.name + "is in the " +  i + " times loop");
          }
      }
      public static void main(String[] args) {
          Runnable runnable1 = new RunThread("thread1");
          Runnable runnable2 = new RunThread("thread2");
          new Thread(runnable1).start();
          new Thread(runnable2).start();
      }
  }
  ```

  程序打印如下

  ```
  当前进程thread1is in the 0 times loop
  当前进程thread2is in the 0 times loop
  当前进程thread1is in the 1 times loop
  当前进程thread2is in the 1 times loop
  当前进程thread2is in the 2 times loop
  当前进程thread1is in the 2 times loop
  ```

  发现也实现了程序并发执行

- 上述两种实现多线程的区别

  | 继承Thread类                  | 实现Runnable接口                          |
  | ----------------------------- | ----------------------------------------- |
  | 重写run方法                   | 重写run方法                               |
  | 创建之后直接使用start启动线程 | 需要创建一个Thread实例来使用start启动线程 |

  由于java的单一继承特性，在使用的时候尽量使用实现Runnable接口的方式







