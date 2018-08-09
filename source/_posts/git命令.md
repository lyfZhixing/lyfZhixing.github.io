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
![image](https://github.com/lyfZhixing/lyfZhixing.github.io/blob/hexo/images/git工作示意图.png?raw=true "git")        
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
git push -f 强制提交  
```
 
若远程仓库有更新，应先更新到本地再提交
##### - 从远程仓库更新代码到本地  
简单命令（不安全）

```
git pull
```

> 覆盖更新本地  
```
git reset --hard，移除所有未提交的本地改动。
git fetch && git reset --hard origin/master，使用github上的仓库覆盖本地，本地提交的也会被移除。
用之前建议先备份。  
除了git reset --hard，  
也可以考虑使用git checkout . 撤销所有文件的修改（新增或删除文件无效）  
git checkout [特定文件路径]针对某些文件撤销修改，多个路径以空格隔开。
另外需注意如果你已经git commit了的话就不能使用checkout了。
```
##### - 分支操作   
```
git branch -a 列出所有分支，当前分支前边有星号表示
git branch 查看本地分支
git branch dev 创建dev
git checkout dev 切换到dev分支
```   

##### - 修改历史提交massage（未push）      

1. 查询git commit日志  
   ```
    git log --oneline -3     //查询最近提交的3条日志，commitid为简短id
   ```     
   ![image](https://github.com/lyfZhixing/lyfZhixing.github.io/blob/hexo/images/gitlog.png?raw=true "git log")   
   图 -- gitlog

2. 若要修改上一次提交的massage直接使用amend    
   ```
   git commit --amend
   ```
   会弹出修改页面，编辑完成之后，^X退出保存，使用git log查看是否修改成功 
3. 若要修改历史提交的massage，按一下步骤来     
   ```
    git rebase -i commitid       //commitid为要修改的commit的下一条提交的id如下图所示
   //即要修改第二条commit的msg，需要地三条commit的id，修改第一条需要第二条的id，以此类推
   ```     
   ![image](https://github.com/lyfZhixing/lyfZhixing.github.io/blob/hexo/images/gitrebase.png?raw=true "rebase命令")    
   图 --修改第一条commit的msg（commitid参考图gitlog）    

4. 修改msg，如图所示：    
   ![image](https://github.com/lyfZhixing/lyfZhixing.github.io/blob/hexo/images/gitpick.png?raw=true "pick")    
   |    |     
   |    |     
   |    |    
   ![image](https://github.com/lyfZhixing/lyfZhixing.github.io/blob/hexo/images/gitreword.png?raw=true "reword")      
   |    |     
   |    |     
   |    |      
   ![image](https://github.com/lyfZhixing/lyfZhixing.github.io/blob/hexo/images/gitmassage.png?raw=true "git massage")      
   |    |     
   |    |     
   |    |    
   ![image](https://github.com/lyfZhixing/lyfZhixing.github.io/blob/hexo/images/gitmassage2.png?raw=true "git msg2")    
   |    |     
   |    |     
   |    |    
   ![image](https://github.com/lyfZhixing/lyfZhixing.github.io/blob/hexo/images/gitmassage3.png?raw=true "git msg3")    
   |    |     
   |    |     
   |    |    
   修改完毕。  


##### - git 删除已经提交只远程仓库的文件     
删除远程不删除本地（推荐）  
```
git rm --cached -r 文件夹或文件名
git commit -m "msg"
git push

```
远程和本地一块删（不推荐）    
```
git rm -r 文件夹或文件名
git commit -m "msg"
git push
```


