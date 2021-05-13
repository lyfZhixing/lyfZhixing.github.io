---
title: JVM概念一
date: 2020-6-18 14:40:10
tags: [java, JVM]
categories: [java基础]
---




# JVM概念一  

- 虚拟机：
  - 系统虚拟机
    - 对物理计算机的仿真： Visual Box， Vmware
  - 程序虚拟机  
    - 专门为执行单个计算机程序而设计： Java虚拟机，执行Java字节码指令   


- Java虚拟机：
  - 跨平台（一次编译到处运行）；
  - 优秀的垃圾回收（自动垃圾回收）；
  - 可靠的即时编译；
  - 自动内存管理   


<!-- more -->


- JVM所处的位置：  

  用户 ---  字节码文件 ---- JVM --------操作系统 ----cpu等   


- JVM的整体结构（hotSpot JVM）   

  - 上层
    - class files
    - 类装载器子系统
  - 中间层 运行时数据区（Runtime Data Area）  
      - 线程共享
        - 方法区（Method Area）  
        - 堆（Heap）  
      - 不共享  
        - Java虚拟机栈（JVM stack）  
        - 本地方法栈（Native Method Stack）  
        - 程序计数器（Program Counter Register）
  - 底层
    - 执行引擎   
      - 解释器  
      - JIT编译器（热点代码即时编译为机器指令并缓存至方法区）  
      - 垃圾回收器GC
    - 本地方法接口
    - 本地方法库
