---
aliases: 
tags:
  - code/concurrent
  - language/java
title: 线程的状态
date created: 2025-04-15 20:04:00
date modified: 2025-04-16 22:04:34
---
# 线程的状态

在 Java 程序中，一个[[高并发]]对象只能调用一次`start()`方法启动新线程，并在新线程中执行`run()`方法。

一旦`run()`方法执行完毕，线程就结束了。因此，Java 线程的状态有以下几种：

- New：新创建的线程，尚未执行；
- Runnable：运行中的线程，正在执行`run()`方法的 Java 代码；
- Blocked：运行中的线程，因为某些操作被阻塞而挂起；
- Waiting：运行中的线程，因为某些操作在等待中；
- Timed Waiting：运行中的线程，因为执行`sleep()`方法正在计时等待；
- Terminated：线程已终止，因为`run()`方法执行完毕。

# 线程通信
线程开始运行，拥有自己的栈空间，就如同一个脚本一样，按照既定的代码一步一步地执 行，直到终止。

java 线程之间的通信方式总共有 8 种，分别是 `volatile`、`synchronized`、`interrupt`、`wait`、`notify`、`notifyAll`、`join`、`管道输入/输出`。

## volatile

Java 支持多个线程同时访问一个对象或者对象的成员变量，由于每个线程可以拥有这个变量的副本（虽然对象以及成员变量分配的内存是在共享内存中的，但是每个执行的线程还是可以拥有一份副本，这样做的目的是加速程序的执行，这是现代多核处理器的一个显著特性）

线程会将内存中的数据，拷贝到各自的本地内存中( 这里的本地内存指的是 cpu cache ( 比如 CPU 的一级缓存、二级缓存等 )，寄存器)。

当某个变量被 volatile 修饰并且发生改变时，volatile 变量底层会通过 lock 前缀的指令，将该变量写会主存，同时利用缓存一致性协议，促使其他线程的本地变量的数据无效，从而再次直接从主存读取数据。

## synchronized

关键字 synchronized 可以修饰方法或者以同步块的形式来进行使用，它主要确保多个线程 在同一个时刻，只能有一个线程处于方法或者同步块中，它保证了线程对变量访问的可见性 和排他性。

同步就好像在公司上班，厕所只有一个，现在一帮人同时想去「带薪拉屎」占用厕所，为了保证厕所同一时刻只能一个员工使用，通过排队互斥实现。

synchronized 的实现原理是对一 个对象的监视器（monitor）进行获取，而这个获取过程是排他的，也就是同一时刻只能有一个 线程获取到由 synchronized 所保护对象的监视器。

监视器锁（Monitor 另一个名字叫管程）本质是依赖于底层的操作系统的 Mutex Lock（互斥锁）来实现的。

在 Java 虚拟机 (HotSpot) 中，Monitor 是基于 C++ 实现的，由 ObjectMonitor 实现的, 几个关键属性：

- \_owner：指向持有 ObjectMonitor 对象的线程
- \_WaitSet：存放处于 wait 状态的线程队列
- \_EntryList：存放处于等待锁 block 状态的线程队列
- \_recursions：锁的重入次数
- count：用来记录该线程获取锁的次数

ObjectMonitor 中有两个队列，\_WaitSet 和 \_EntryList，用来保存 ObjectWaiter 对象列表( 每个等待锁的线程都会被封装成 ObjectWaiter 对象)，\_owner 指向持有 ObjectMonitor 对象的线程，当多个线程同时访问一段同步代码时，首先会进入 \_EntryList 集合，当线程获取到对象的 monitor 后进入 \_Owner 区域并把 monitor 中的 owner 变量设置为当前线程同时 monitor 中的计数器 count 加 1。

若线程调用 wait() 方法，将释放当前持有的 monitor，owner 变量恢复为 null，count 自减 1，同时该线程进入 WaitSet 集合中等待被唤醒。

## 等待/通知机制

一个线程修改了一个对象的值，而另一个线程感知到了变化，然后进行相应的操作。

Java 多线程的等待/通知机制是基于`Object`类的`wait()`方法和`notify()`, `notifyAll()`方法来实现的。

等待/通知机制，是指一个线程 A 调用了对象 O 的 wait()方法进入等待状态，而另一个线程 B 调用了对象 O 的 notify()或者 notifyAll()方法，线程 A 收到通知后从对象 O 的 wait()方法返回，进而 执行后续操作。

```java
 public class TestSync {  
     public static void main(String[] args) {  
         // 定义一个锁对象  
         Object lock = new Object();  
         List<String>  list = new ArrayList<>();  
         // 实现线程A  
         Thread threadA = new Thread(() -> {  
             synchronized (lock) {  
                 for (int i = 1; i <= 10; i++) {  
                     list.add("abc");  
                     System.out.println("线程A向list中添加一个元素，此时list中的元素个数为：" + list.size());  
                     try {  
                         Thread.sleep(500);  
                     } catch (InterruptedException e) {  
                         e.printStackTrace();  
                     }  
                     if (list.size() == 5)  
                         lock.notify();// 唤醒B线程  
                 }  
             }  
         });  
         // 实现线程B  
         Thread threadB = new Thread(() -> {  
             while (true) {  
                 synchronized (lock) {  
                     if (list.size() != 5) {  
                         try {  
                             lock.wait();  
                         } catch (InterruptedException e) {  
                             e.printStackTrace();  
                         }  
                     }  
                     System.out.println("线程B收到通知，开始执行自己的业务...");  
                 }  
             }  
         });  
         // 需要先启动线程B  
         threadB.start();  
         try {  
             Thread.sleep(1000);  
         } catch (InterruptedException e) {  
             e.printStackTrace();  
         }  
         // 再启动线程A  
         threadA.start();  
     }  
 }
```

## ThreadLocal 的使用
ThreadLocal 是 Java 并发包（`java.lang`）中的一个类，**用于为每个线程创建独立的变量副本**，实现线程间的数据隔离。

它通过空间换时间的方式，避免多线程共享变量时的同步开销，适用于需要线程私有数据的场景。

在 Web 应用中通过 `ThreadLocal` 传递用户身份信息是典型的生产级场景

```java
public class UserContext {
    // 使用静态内部类实现懒加载，保证线程安全
    private static final ThreadLocal<UserInfo> currentUser = new ThreadLocal<>();

    public static void set(UserInfo user) {
        currentUser.set(user);
    }

    public static UserInfo get() {
        UserInfo user = currentUser.get();
        if (user == null) {
            thrownew IllegalStateException("User not found in current thread context");
        }
        return user;
    }

    public static void clear() {
        currentUser.remove(); // 必须显式清理防止内存泄漏
    }
}
```