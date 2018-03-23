---
title: mysql修改root密码
date: 2018-03-23 15:18:08
tags: [mysql]  
categories: 
- 环境搭建  
- 数据库  
- MySQL
---
> ##### 如何解决MySQL5.7忘记密码（修改密码）的问题  

---
<!-- more -->
1. 停止MySQL的服务
2. cmd命令窗口，cd到MySQL的bin目录 
3. 执行命令，若命令无法运行则在最前边加上  .\

```
该命令通过跳过权限安全检查，开启mysql服务，这样连接mysql时，可以不用输入用户密码
mysqld --defaults-file="C:\ProgramData\MySQL\MySQL Server 5.7\my.ini" --skip-grant-tables
```
my.ini配置文件默认和安装目录不在一起，ProgramData是一个隐藏文件夹，需要设置隐藏文件为可见
4. 上一条命令会一直运行，重新打开一个cmd窗口进行操作：（还是在bin目录下）  
- 连接mysql
```
mysql -uroot -p   
```
- 不用输入密码，直接回车  
- 出现登录成功的信息  
 ![image](\images\post-images\2018-03-23_094948.png) 
- 查看数据库  

```
show databases;
```
- 切换到mysql数据库

```
use mysql;
```
修改root用户密码：

```
UPDATE user SET authentication_string=PASSWORD('password') WHERE User='root'; 
```
- 刷新权限（必须）  
```
flush privileges;
```
- 退出登录  
```
quit
```
- 重新登录
```
mysql -uroot -p
```
> ps:重启服务时报错：本地计算机上的mysql服务启动后停止，某些服务在未由其他服务或程序使用时将自动停止。  
> 可能是因为mysqld.exe在后台运行，在任务管理器杀掉即可

