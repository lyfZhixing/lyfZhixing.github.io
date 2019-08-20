---
title: 初识Netty
date: 2019-08-20 22:29:37
tags: [Netty]
categories:
- Netty
- 概念及体系结构
---
&emsp;&emsp;Netty是一个java网络编程框架，可以很好地兼顾到高并发和高性能，并且开发高效，封装了很多复杂的API，且原生支持异步和多线程。    
&emsp;&emsp;Netty 的核心组件包括：    
- Channel（数据出站和入站的载体，可以被打开或关闭，连接或断开）
- 回调（实现异步的一种机制）
- Future（实现异步的另一种机制）
- 事件和ChannelHandler(开发者需要关注的地方，出站，入站，日志记录，数据转换等操作以及业务逻辑处理)
