---
title: git命令
date: 2018-03-22 10:11:37
tags: [git]
categories: 
- 版本控制
- GIT
---
> **GIT （分布式版本控制系统）**  
 以前一直用GitHub for Windows 提交代码，都在图形界面操作。现在练习使用git命令来进行远程仓库拉取、本地仓库更新、工作空间提交等等（图一简要的描述了git工作的流程）。  
<!-- more -->  
![image](/images/git工作示意图.png)  
*++图一++*  
##### - 从GitHub.com克隆项目，复制ssh地址：  
![image](/images/git-clone.png)  
*++图二++*  
命令窗口cd 到在本地工作空间地址，输入命令：
```
git clone ~~ssh地址~~
```

![image](/images/git-clone2.png)  
*++图三++*  
##### - 提交本地代码到远程仓库  
> 查看本地分支文件信息，确保更新时不产生冲突  

```
git status
```
可能会提示一大堆Untracked文件  
Git在未进行commit操作之前，存在三种状态：Untracked files，Changes not staged for commit及Changes to be committed。
Untracked files就是你必须把某些文件放到Git工作目录中，但又不能提交它们，比如保存了数据库密码的配置文件。 
可以在.gitignore文件中设置忽略。
> 提交到暂存区：
```
git add ~~文件名~~或git add .
```
 
在使用时经常会遇到以下错误，  
![image](/images/git-add.png)  
*++图四++*  
解决办法： 在命令最后加一个空格  
> 提交到本地仓库：
```
git commit -m "~~版本描述~~"
```
   
> 提交到远程仓库： 
```
git push origin ~~分支名~~   
```
 
若远程仓库有更新，应先更新到本地再提交
##### - 从远程仓库更新代码到本地  
简单命令（不安全）

```
git pull
```

> 



