---
title: 并发与JMM（1）——原子性，可见性，有序性
date: 2020-5-5 13:42:15
tags: [java, 并发, JMM]
categories: [java基础]
---

# 并发与JMM

java多线程技术可以大幅提升Java应用性能，但同时也会带来一些问题，比如处理开发并发程序会必串行程序复杂的多，并发程序会遇到一些串行程序没有的问题，如数据访问的一致性和安全性。那么 java程序并发下数据访问的一致性和安全性该如何保障？

JMM(Java内存模型)定义了一种规则，用来保证多个线程间可以有效地、正确地协同工作。

在了解JMM之前我们先熟悉一下多线程的原子性、可见性、有序性，这些概念对学习JMM很重要。

<!-- more -->

## 原子性（Atomicity）

原子性就是指一个操作或组合操作是不可中断的，这个操作或操作组合在一个线程的执行过程中不会被其他线程打扰中断。

常见的符合原子性的操作有，给一个静态全局变量int i 的赋值操作。

> tips: 在32位系统（或JVM）中对一个静态全局变量long i 的赋值操作是非原子性的，因为long是64位，在32位系统中赋值需要两步操作，而这个组合操作是非原子性的，在多线程中会被互相干扰，导致读写问题。

## 可见性（Visibility）

可见性是指在多线程中的一个线程修改了某一共享变量的指，其他的线程能否立刻发现这个修改。

举个例子：

![可见性例图](https://gitee.com/lyfZhixing/draw/raw/master/multi-Thread/png/%E5%8F%AF%E8%A7%81%E6%80%A7.png)

上边的例子出现可见性问题是cpu的缓存优化导致的，但导致可见性问题的原因不止缓存优化，还有指令重排，编译器优化等。

## 有序性（Ordering）

有序性是指代码执行按照我们期望的顺序执行。但是在多线程中如果处理不当， 代码的执行顺序就可能背离我们的期望。导致乱序执行的原因是：在程序执行时Java虚拟机（或者系统）可能会进行指令重排以提高程性能，但也会带来乱序问题。

举个例子：

```

class Ordering() {
  int a = 0;
  boolean flag = false;
  // Thread A run first
  public void writer() {
    a = 1;
    flag = true;
  }

  // then Thread B run
  public int reader() {
    if (flag) {
      return a;
    }
  }
}

```

线程A先执行writer() 接着线程B执行reader()，那么线程B返回的值一定是 1 吗？

答案是不一定，因为如果发生指令重排，结果就不一定了，如图：

![可见性例图](https://gitee.com/lyfZhixing/draw/raw/master/multi-Thread/png/有序性.png)
