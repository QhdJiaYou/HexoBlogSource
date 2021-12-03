---
title: git指令
date: 2020-11-06 17:50:43
---



1、查看本地git用户信息

git config user.name

git config user.email

2、配置本地git用户信息

git config --global user.name "username"

git config --global user.email "email"

3、多账户配置流程，主要是为多个邮箱账号生成ssh密钥对
https://www.jianshu.com/p/b6bd61e1bc5f


<!-- ![avatar](https://github.com/QhdJiaYou/Image/blob/master/gitOverview.png) -->


新建分支      git branch dev

切换分支      git checkout dev

创建并切换分支      git check -b dev

查看当前git分支     git branch

查看所有分支          git branch -a

合并指定分支dev到当前分支        git merge dev

删除分支      git branch -d dev

每次更新代码都要做的操作

自己的分支add -> commit -> push

切换到dev分支checkout -> merge -> push

git pull 相当于git fetch和git merge



在本地新建一个tmp分支，并将远程origin仓库的master分支代码下载到本地tmp分支

git fetch origin master:tmp 

来比较本地代码与刚刚从远程下载下来的代码的区别

git diff tmp 



***版本回退***

在git中每次commit都会记录相应的版本号id

git log  查看每次commit的版本id



想要版本回退到之前commit的某个版本

git reset —hard commitID  回退到指定的版本

git reset —hard HEAD^      回退到上一个版本

git reset —hard HEAD^^     回退到之前两个版本

git reset —hard HEAD~n    回退到之前n个版本



回退操作之后在git log中已经查看不到回退之前的版本了，这时候需要使用

git reflog   可以记录每次命令的版本id

这样我们就可以拿着版本id使用回退的指令回去之前的版本



git commit 跳过检查的参数--no-verify

```git
git commit --no-verify -m "修改..."
```



##### 将已有项目添加到远程git仓库步骤如下：

1、在github中创建与本地相同名字的仓库

2、在本地添加远程仓库, 给远程仓库设置别名为origin

git remote add origin 远程仓库地址(git@github.com:QhdJiaYou/TestJS.git)

3、查看远程仓库

git remote -v

4、git pull 仓库别名 仓库分支名 

【注】远程仓库和本地仓库分别创建的时候直接pull会出现错误，refusing to merge unrelated histories，这个时候就要使用--allow-unrelated-histories参数来解决(可以合并两个独立启动仓库的历史)，使用 git clone指令初始化本地仓库就不会出现这种问题

git pull origin master --allow-unrelated-histories

5、git add   ->      git commit

可以在.gitignore中添加需要忽略上传的文件，方便通过git add .添加

6、git push 仓库别名 仓库分支名



##### git clone 指令

git clone [-b 分支名] 远程库地址  本地目录名