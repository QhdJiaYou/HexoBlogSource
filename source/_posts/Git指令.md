---
title: git指令
date: 2020-11-06 17:50:43
---


## 一、git配置相关操作
### 查看本地git用户信息

git config user.name

git config user.email

### 配置本地git用户信息

git config --global user.name "username"

git config --global user.email "email"

生成密钥对：`ssh-keygen -t rsa -C "邮箱"`

复制公钥内容： `clip < ~/.ssh/id_rsa.pub`

【备注】[多账户配置流程](https://www.jianshu.com/p/b6bd61e1bc5f)，主要是为多个邮箱账号生成ssh密钥对

### 查看不同级别的git config

|     级别    | 文件存储位置 | 查看命令 | 优先级 |
| :-------: | ------ | ------ | ------- |
| 系统级别 | /etc/gitconfig | git config --system --list |最低 |
| 用户级别 | ~/.gitconfig | git config --global --list |中 |
| 项目级别 | .git/config | git config --local --list |最高 |

**【备注】所以当我们想单独配置某个项目的user.name user.email 以及远程仓库地址, 都可以通过修改项目的.git/config文件实现，可以实现一台电脑同时使用多个github账号**

## 二、远端拷贝

### 拉取git仓库

#### `git fetch origin master:tmp`
在本地新建一个tmp分支，并将远程origin仓库的master分支代码下载到本地tmp分支

#### `git clone [-b 分支名] 远程库地址 本地目录名`
将远程仓库拷贝到本地指定目录，并新建分支


#### `git pull 仓库别名 仓库分支名称`
拉取远端更新内容

### 将已有项目添加到远程git仓库

#### `git remote add origin 远程仓库地址`
给本地项目添加远程仓库地址

#### `git remote -v`
查看远程仓库

#### `git push 仓库别名 仓库分支名称`
将本地更新推送到远端

#### `git pull origin master –allow-unrelated-histories`

<font color=Blue>**【备注】远程仓库和本地仓库分别创建的时候直接pull会出现错误，refusing to merge unrelated histories，这个时候就要使用–allow-unrelated-histories参数来解决(可以合并两个独立启动仓库的历史)**</font>

## 三、本地的工作区和版本库

### 版本库包括暂存区和本地分支

![avatar](https://github.com/QhdJiaYou/Image/blob/master/gitOverview.png?raw=true)

git add把文件添加进去，实际上就是把**文件修改**添加到**暂存区**；

git commit提交更改，实际上就是把**暂存区**的所有内容提交到**当前分支**。

### 恢复暂存区的指定文件到工作区

```shell
git checkout [fileName]
git checkout -- [fileName] // -- 表示后面的内容是文件，是为了避免指令和文件名相同时的冲突，例如rm -f 和rm -- -f，后面的指令是删除名为-f的文件
git checkout . //恢复暂存区的所有文件到工作区
```
### 删除本地分支

```shell
git branch -D branchName  或 git branch -d branchName  // D是强制删除
```
### 比较差异Diff

```shell
git diff [filePath] // 比较working和stage
git diff HEAD // 显示working与commit的差异
git diff --cached：是查看stage与commit的差别的
git diff HEAD^ // 是比较working与上次commit
git diff HEAD~n
```
### git新增——git switch和git restore

```shell
git checkout -b [branchName] 等同于 git switch -c [branchName]
git restore -- [fileName] 等同于 git checkout -- [fileName]
```
### 重置代码

[git reset和git revert操作的区别](https://juejin.cn/post/6844903614767448072)

实现手段不同，git reset是通过操作修改当前commit指针指向，操作后，某次commit之后的所有commit都会被去掉

而git revert是通过新增一次commit来抵消之前某次commit的修改，原本的修改仍然还在，可以达到只去除某一次commit的效果

```shell
git reset --hard // 修改指针123
git reset --mixed  // 修改指针12，默认值
git reset --soft //修改指针1

//例子
git reset --hard HEAD^  // 所有地方都修改成上一次commit
```
![avatar](https://github.com/QhdJiaYou/Image/blob/master/gitResetWay.png?raw=true)

## 四、git实战经验
### git pull --rebase

使用场景: 拉取远端代码, 又不想产生新的merge commit记录

使用时两个注意点:

* 执行 git pull --rebase 的时候必须保持本地目录干净。即：不能存在状态为 modified 的文件。（存在Untracked files是没关系的）
* 如果出现冲突，可以选择手动解决冲突后继续 rebase，也可以放弃本次 rebase

解决本地文件修改

* 如果本次修改已经完成，则可以先提交（commit）一下
* 如果本次修改尚未完成，则可以先贮藏：git stash

```shell
# 把本地发生改动的文件贮藏一下
git stash 
# 把远程最新的 commit 以变基的方式同步到本地
git pull --rebase
# 如果存在冲突,一般是多人改了同一个地方, 手动修改冲突文件后执行
$ git add 冲突文件
$ git rebase --continue
# 也可以执行git rebase --abort终止合并操作继续执行
# 把本地的 commit 推送到远程
git push 
# 把本地贮藏的文件弹出，继续修改
git stash pop
```

**【备注】使用rebase出现冲突,有一种情况是要避免的**

如果你本地N次commit都修改了同一个地方,且与远端被另外一个同学修改的地方有冲突

那么这个时候就需要手动解冲突N次

解决办法是, 直接使用git pull(git fetch + git merge)

或者本地先将多次commit合并, 再使用git pull --rebase

### 打tag

```shell
git tag r-201013-8a2ceb-quhandong
git push origin r-201013-8a2ceb-quhandong
```

### 常用命令行指令

```shell
ctrl + w 往回删除一个单词，光标放在最末尾
ctrl + u 删除光标以前的字符
ctrl + k 删除光标以后的字符
ctrl + a 移动光标至的字符头
ctrl + e 移动光标至的字符尾
ctrl + l 清屏
```









