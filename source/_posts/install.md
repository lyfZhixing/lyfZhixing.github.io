---
title: docker的安装及换源
date: 2020-5-5 13:43:21
tags: [docker]   
categories: [docker]
---

# docker的安装及换源  
- 系统： centos 8   
- 安装方式：yum
- 参考： [docker官方文档](https://docs.docker.com/engine/install/centos/)   
---
- [卸载旧版本](#remove)   
- [安装](#set)  
- [更换到国内源](#replace)

## <span id="remove">卸载旧版本</span>   
```
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```
---
## <span id="set">设置仓库</span>   
1. 安装依赖工具   
```
sudo yum install -y yum-utils
```   
2. 设置安装仓库   
```
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```  
3. 选择开启或关闭test/nightly库（测试/开发）   
开启
```  
sudo yum-config-manager --enable docker-ce-nightly
sudo yum-config-manager --enable docker-ce-test
```   
关闭  
```   
sudo yum-config-manager --disable docker-ce-nightly
sudo yum-config-manager --disable docker-ce-test
```   
docker默认关闭test/nightly库，只开启stable（正式版），建议关闭   
----
##  安装docker引擎   
1. 安装最新版本的docker引擎和容器，如果想要安装指定版本跳过此步骤，进行第二步。建议跳过   
```
sudo yum install docker-ce docker-ce-cli containerd.io
```   
2. 安装特定版本的docker    
a. 列出docker的版本   
```
yum list docker-ce --showduplicates | sort -r
```  
b. 选择合适版本进行安装，我这里选择`19.03.8`   
```
sudo yum install docker-ce-19.03.8 docker-ce-cli-19.03.8 containerd.io
```
> 这里可能遇到错误：
```
Error:
 Problem: package docker-ce-3:19.03.8-3.el7.x86_64 requires containerd.io >= 1.2.2-3,
but none of the providers can be installed
```
解决办法： 去下载安装合适版本的[containerd.io](https://download.docker.com/linux/fedora/30/x86_64/stable/Packages/)，我这里选择的是`1.2.13-3.1`
```
yum install https://download.docker.com/linux/fedora/30/x86_64/stable/Packages/containerd.io-1.2.13-3.1.fc30.x86_64.rpm
```
安装完成`containerd.io`之后重新执行第二步的操作即可


---
## 启动docker  
```
sudo systemctl start docker
```   
> 验证docker是否启动成功，可以跑一个`hello-world`试一下(本地如果没有该镜像，会自动去仓库拉取)   
```
sudo docker run hello-world
```
![成功截图](https://gitee.com/lyfZhixing/draw/raw/master/docker/2020-05-05_111003.png)

## <span id="replace">换源</span>  
国内源有很多选择，网易，中科大，阿里等，我这里选择的是`ustc`中科大的docker源([ustc官方文档](https://lug.ustc.edu.cn/wiki/mirrors/help/docker))    
- 使用`daemon.json`进行配置（此文件在`/etc/docker`目录下，如果没有可以新建一个）
```
{
  "registry-mirrors": ["https://docker.mirrors.ustc.edu.cn"]
}
```
- 重启docker服务   
```
sudo systemctl restart docker
```
