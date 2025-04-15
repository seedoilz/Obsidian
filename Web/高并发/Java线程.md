---
aliases: 
tags:
  - code/concurrent
  - language/java
title: Java线程
date created: 2025-04-15 20:04:00
date modified: 2025-04-15 20:04:46
---
## 线程的状态
在 Java 程序中，一个[[线程]]对象只能调用一次`start()`方法启动新线程，并在新线程中执行`run()`方法。

一旦`run()`方法执行完毕，线程就结束了。因此，Java 线程的状态有以下几种：
- New：新创建的线程，尚未执行；
- Runnable：运行中的线程，正在执行`run()`方法的 Java 代码；
- Blocked：运行中的线程，因为某些操作被阻塞而挂起；
- Waiting：运行中的线程，因为某些操作在等待中；
- Timed Waiting：运行中的线程，因为执行`sleep()`方法正在计时等待；
- Terminated：线程已终止，因为`run()`方法执行完毕。


