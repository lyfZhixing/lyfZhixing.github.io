---
title: 并发与JMM（2）——指令重排与Happen-Before规则
date: 2020-5-5
tags: [java, 并发, JMM]
categories: [java基础]
---

# 指令重排    

在多线程的有序性里边我们已经初步了解了指令重排，但是需要注意的是，在单个线程里指令执行的顺序一定是一致的，也就是说指令重排必须保证串行语义的一致性，不能因为指令重排导致串行语义的逻辑发生问题。    
> tips： 指令重排可以保证串行语义的一致，但无法保证多线程间的语义一致      

为什么要进行指令重排？ 无他，为了性能。     

在Cpu执行一条指令时，简单来说可以分为如下几步：   

1. 取指令  IF    
2. 译码和取寄存器操作数  ID
3. 执行或者有效地址计算  EX
4. 存储器访问  MEM   
5. 写回  WB   

CPU在实际工作中执行一组指令，为了性能，并不是等前一条指令执行完毕再依次执行下一条指令，而是采用流水线的工作原理：    
![指令流水线](https://gitee.com/lyfZhixing/draw/raw/master/multi-Thread/png/指令流水线.png)    

在实际执行一个程序时，流水线之间的衔接不可能总是完美的，如 A = B + C 这个操作：  

![A=B+C](https://gitee.com/lyfZhixing/draw/raw/master/multi-Thread/png/A=B+C.png)    

图中的红叉就表示流水线中断，CPU进入等待，一旦发生中断，这个时间点执行的所有步骤都会向后延（最后的SW操作执行到ID也进入等待）极大影响效率。     
为了避免这种情况发生就需要进行指令重排，在不影响串行语义的情况下对指令进行重新排序，尽可能提高效率。

# Happen-Before规则   

Java虚拟机和执行系统再进行指令重排时需要遵守Happen-Before规则。

   **Happen-Before规则是用来阐述操作之间的内存可见性。用来保证正确同步的多线程程序的执行结果不被改变**

望文生义，Happen-Before很容易理解为前一个操作执行完毕再执行后一个操作，即两个遵守happen-before规则的操作是按顺序先后执行的，这样理解对，但不完全对。   
站在开发者的角度来说，这样理解好像没什么毛病，毕竟系统执行程序的结果是符合预期的（语义未改变）；但是对于Java虚拟机来说，只要保证第一个操作的执行结果对第二个操作可见即可，如果指令重排后的执行结果和happen-before规则执行的结果一致则允许进行重排。    

一张图理解happen-before:    
![一张图理解happen-before](https://gitee.com/lyfZhixing/draw/raw/master/multi-Thread/png/happen-before.png)    

Happen-Before规则的基本原则：    

1. 程序顺序原则： 单个线程内保证语义的串行性。  
2. volatile规则： volatile变量的写，先发生与读，保证volatile变量的可见性。    
3. 锁规则： 解锁（unlock）必须发生再随后的加锁（lock）前。    
4. 传递性： A先于B， B先于C ，那么A必然先于C。    
5. 线程的start()方法先于它的每一个动作。    
6. 线程的所有操作优先于线程的终结（Thread.join()）     
7. 线程的中断（interrupt()） 先于被中断线程的代码。
8. 对象的构造函数执行、结束优先于finalize() 方法。
