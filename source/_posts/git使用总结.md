---
title: git使用总结
date: 2017-02-26 23:13:08
tags: [git]
categories: 技术
---
## 注意
git status要经常用  

## 基本操作
#把文件修改添加到暂存区  
git add  file  
<!-- more -->

#一次添加多个文件用空格  
git add a.txt b.cpp  

#把所有文件修改添加到暂存区  
git add  .  
git add --all  

#把暂存区的所有内容提交到当前分支  
git commit  -m "backup_message"  

## 日志
#查看日志,不包括回退操作记录  
git log  

#查看简要日志（只有id和备注信息）  
git log --pretty==online  

#查看每一次操作，包括回退，包含id、HEAD、操作和备注信息  
git reflog  

## 比较
#工作区和暂存区的比较  
git diff file  

#暂存区和分支的比较  
git diff --cached  

## 删除
#删除本地和远程的文件  
git rm file  

#删除缓冲区文件  
git rm -r --cached file  

## 回退
#回退到上一个版本（HEAD^^上上个版本，HEAD~100上100个版本）   
git reset --hard HEAD^  

#回退到指定版本  
git reset --hard commit_id  

#让文件回到最近一次commit或add的状态  
git checkout -- file  

#从暂存区移除重新放回工作区  
git reset HEAD file  

## 分支
#创建分支
git branch branch_name  

#创建并切换到分支  
git checkout -b branch_name  

#切换到分支  
git checkout branch_name  

#合并分支到当前分支，提示冲突后需要手动修改冲突部分内容，只保留冲突部分的一个版本，然后再add、commit  
git merge branch_name  

#删除分支  
git branch -d branch_name  

#非fast forward模式合并，保留历史分支合并信息  
git merge --no-ff -m "backup_message" branch_name  

## 远程
#显示所有关联的远程仓库  
git remote -v  

#没有本地仓库的情况下，复制远程仓库到本地  
git clone remote_repo_name  

#关联远程仓库（在远程仓库第一次创建了还没有关联时）（origin是远程库的一个别名，可自由设置）  
git remote add origin remote_repo_name  

#把远程最新代码更新合并到本地（已经建立了关联情况下）（fetch+merge）  
git pull origin master  

#从远程获取最新版本到本地，不会自动merge  
git fetch origin master  

#比较本地的master分支和origin/master分支的差别  
git log -p master origin/master  

#合并（fetch到本地的）远程分支  
git merge origin/master  

#建立本地分支和远程分支的连接关系（这样以后就可以直接git pull，git push）  
git branch --set-upstream branch_name origin/branch_name  

#第一次向远程仓库提交代码，之后可以省略-u  
git push -u origin master  

## 标签
tag就是一个让人容易记住的有意义的名字，它跟某个commit绑在一起  

#对最近一次commit打标签  
git tag tag_name  

#对特定commit打标签  
git tag commit_id tag_name  

#对特定commit打标签并添加说明  
git tag -a tag_name -m "backup_message" commit_id  

#查看标签信息  
git show tag_name  

#删除标签  
git tag -d tag_name  

#推送本地标签到远程  
git push origin tag_name  

#推送全部本地标签  
git push origin --tags  

## 临时存储
#临时存储，可以做完别的之后过来恢复  
git stash  

#查看stash记录  
git stash list  

#恢复stash，并不删除记录  
git stash apply  

#删除stash  
git stash drop  

#恢复同时删除stash记录  
git stash pop  

## 分支管理策略
master分支仅用来发布版本，平时在dev分支干活  
修复bug，通常会创建新的bug分支修复，然后合并，手头的工作先stash  
添加新功能，最好新建一个feature分支，完成后合并
## 参考
[Git教程-廖雪峰的官方网站](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)  
[常用 Git 命令清单-阮一峰的网络日志](http://www.ruanyifeng.com/blog/2015/12/git-cheat-sheet.html)