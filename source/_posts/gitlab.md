---
title: 在docker中安装部署gitlab
date: 2020-5-5 14:42:15
tags: [docker, gitlab, docker-compose]   
categories: [docker]
---


# 在docker中安装部署gitlab  
- 方法： docker-compse    

---  

## 安装`docker-compse`    

1. 下载docker-compse，这里下载的是`1.25.5`版本，如有其它版本需求可自行替换   
```
sudo curl -L "https://github.com/docker/compose/releases/download/1.25.5/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

2. 授权使其可执行  
```
sudo chmod +x /usr/local/bin/docker-compose
```

> 参考：[docker docs](https://docs.docker.com/compose/install/)，如安装遇到问题，也可在其中查找解决办法   
验证是否安装成功：   
```
docker-compose
```

![成功截图](https://gitee.com/lyfZhixing/draw/raw/master/docker/2020-05-05_122101.png)

## 编写`docker-compose.yml`   
1. 新建文件夹gitlab`$ mkdir gitlab`，进入文件夹;  
2. ~~新建文件docker-compose.yml`$touch docker-compose.yml`~~,在win系统新建文件docker-compose.yml并编辑保存后使用lrzsz工具上传至服务器  
```
version: '3'
services:
    web:
      image: 'twang2218/gitlab-ce-zh'
      restart: always
      hostname: '192.168.141.200'
      environment:
        TZ: 'Asia/Shanghai'
        GITLAB_OMNIBUS_CONFIG: |
          external_url 'http://192.168.141.200'
          gitlab_rails['gitlab_shell_ssh_port'] = 2222
          unicorn['port'] = 8888
          nginx['listen_port'] = 80
      ports:
        - '80:80'
        - '443:443'
        - '2222:22'
      volumes:
        - ./config:/etc/gitlab
        - ./data:/var/opt/gitlab
        - ./logs:/var/log/gitlab
```

> 参考： [千峰李哥](http://www.qfdmy.com/#/courses/lesson/1251946386275790850/1253811278091128834)   

3. 构建镜像(加`-d`可以静默执行)   
```
sudo docker-compose up [-d]
```

在浏览器输入服务器的ip(docker-compose.yml已配置映射至80端口)即可访问gitlab   

![部署成功](https://gitee.com/lyfZhixing/draw/raw/master/docker/2020-05-05_143737.png)
