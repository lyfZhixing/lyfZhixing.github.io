---
title: nginx403错误
date: 2018-08-07 18:01:01
tags: [nginx,部署]  

---
## 安装nginx环境  
1.安装：  
sudo apt install nginx  
2.启动：  
service nginx start  
3.编辑欢迎页  
cd /var/www/html/  
sudo vi index.nginx-debian.html   
## 将项目部署至nginx(前端)   
1.打包项目，只用dist文件夹   
2.找到nginx.conf文件，配置server如下图所示     

![image](https://github.com/lyfZhixing/lyfZhixing.github.io/blob/hexo/images/nginx.png?raw=true "nginx配置")      

某些版本可能不在nginx.conf文件中配置，但是可以从外部引入。   
3.重新加载nginx配置：    
sudo nginx -s reload    
4.重启nginx：    
service nginx restart    
5.在浏览器输入localhost:8090(就上图中的配置而言)，即可进行访问   
## nginx浏览器报403错误   
1.查看日志，/var/log/nginx/error.log,报错：Permission denied    
2.错误原因可以从下面四步进行排查   
2.1 - 查看nginx的启动用户和使用用户是否一致，一般默认用户并不是root  
2.1.1 - 运行命令：   
```
ps aux | grep "nginx: worker process" | awk '{print $1}'
```
 
2.1.2 - 修改nginx.conf中的user和使用用户一致，即修改为本机用户名  
![image](https://github.com/lyfZhixing/lyfZhixing.github.io/blob/hexo/images/nginx403.png?raw=true "nginx配置")    

2.2 - 没有index.html，index.htm等默认启动页，注意路径   
2.3 - 权限问题，nginx没有操作项目目录的权限  
2.3.1 - 修改项目目录的读写权限，使用chmod命令
2.3.2 - 把nginx的启动用户修改为目录所属用户   
2.3.3 - 换启动用户有权限操作的目录  
2.4 - SELinux设置为开启状态（enabled）的原因。  
2.4.1 - 查看当前selinux的状态。  
```
/usr/sbin/sestatus  
```    
2.4.2 - 将SELINUX=enforcing 修改为 SELINUX=disabled 状态。  
```
vi /etc/selinux/config

#SELINUX=enforcing

SELINUX=disabled
```    
2.4.3 -   重启生效。reboot