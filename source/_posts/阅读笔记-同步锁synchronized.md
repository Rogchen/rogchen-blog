
title: 阅读笔记->同步锁synchronized/volatile

tags:
	[syschronized,lock,volatile]

---
## synchronized 关键字原理
众所周知 synchronized 关键字是解决并发问题常用解决方案，有以下三种使用方式:

* 同步普通方法，锁的是当前对象。在范围操作符之后，返回类型声明之前使用。每次只能有一个线程进入该方法，此时线程获得的是成员锁。

<!--more-->

```
public synchronized void syncMethod() {
    //doSomething
}
```

* 同步静态方法，如果当前线程进入，那么其他线程在该类中所有操作不能进行，包括静态变量和静态方法，此时当前线程获得的是对象锁。
```public class syncClass {
    public void method() {
        synchronized(syncClass.class) {
            //doSomething
        }
    }
}
```

* 同步块，锁的是 () 中的对象。每次只能有一个线程进入该代码块，此时线程获得的是成员锁。
```
public int synMethod(int arg){
    synchronized(arg) {
        //doSomething
    }
}
```

实现原理： JVM 是通过进入、退出对象监视器( Monitor )来实现对方法、同步块的同步的。

具体实现是在编译之后在同步方法调用前加入一个 monitor.enter 指令，在退出方法和异常处插入 monitor.exit 的指令。

其本质就是对一个对象监视器( Monitor )进行获取，而这个获取过程具有排他性从而达到了同一时刻只能一个线程访问的目的。

而对于没有获取到锁的线程将会阻塞到方法入口处，直到获取锁的线程 monitor.exit 之后才能尝试继续获取锁。

锁优化

synchronized 很多都称之为重量锁，JDK1.6 中对 synchronized 进行了各种优化，为了能减少获取和释放锁带来的消耗引入了偏向锁和轻量锁。

## volatile java关键字
volatile 关键字只能保证可见性，顺序性，不能保证原子性。
volatile 关键字的作用是禁止指令的重排序，强制从公共堆栈中取得变量的值，而不是从线程私有的数据栈中取变量的值。
volatile与synchronized的区别如下：

volatile 不会发生线程阻塞，而 synchronized 会发生线程阻塞。
volatile 只能修饰变量，而 synchronized 可以修饰方法、代码块等。
volatile 不能保证原子性（不能保证线程安全），而 synchronized 可以保证原子性。
volatile 解决的是变量在多线程之间的可见性，而 synchronized 解决的是多线程之间访问资源的同步性。
``` 双重检查单例
   public class Singleton {
        private static volatile Singleton singleton;

        private Singleton() {
        }

        public static Singleton getInstance() {
            if (singleton == null) {
                synchronized (Singleton.class) {
                    if (singleton == null) {
                        singleton = new Singleton();
                    }
                }
            }
            return singleton;
        }

    }
```

## lock
  synchronized是隐式锁，在需要同步的对象中加入此控制，而lock是显示锁，需要显示指定起始位置和终止位置。


使用lock时在finally中必须释放锁，不然容易造成线程死锁；而使用synchronized时，获取锁的线程会在执行完同步代码后释放锁（或者JVM会在线程执行发生异常时释放锁）。
使用lock时线程不会一直等待；而使用synchronized时，假设A线程获得锁后阻塞，其他线程会一直等待。
lock可重入、可中断、可公平也可不公平；而synchronized可重入但不可中断、非公平。
