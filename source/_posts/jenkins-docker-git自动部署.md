---
title: jenkins+docker+git自动部署
date: 2018-10-07 10:08:14
tags: [jenkins,docker,git,部署]   
categories：  
- 微服务  
- 自动部署
---
## - 自动部署的流程    
![image](https://github.com/lyfZhixing/lyfZhixing.github.io/blob/hexo/images/Jenkins.png?raw=true "Jenkins自动部署")    
<!-- more -->     
## - 前端项目使用Jenkins+git自动部署   
1.从git上拉取项目   
2.命令构建   
```
cd $WORKSPACE/Admin-UI/     //从git拉取到本地的路径
npm install --registry=https://registry.npm.taobao.org      //在本地安装依赖
npm run build:prod          //构建打包
scp -r dist/* uname@10.10.10.111:/home/nginx   //部署到服务器
```
3.立即构建   
