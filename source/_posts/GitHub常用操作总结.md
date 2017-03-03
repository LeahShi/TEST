---
title: Git和GitHub总结
date: 2017-02-19 21:06:17
tags: [Hexo,NexT,stylus]
categories: work
---
![GitHub slogan](/images/github_banner.jpg)

<!-- more -->

> 在过去一年中可能GitHub接触的相对较多，不过也仅限于在上线download一些demo。
> 公司里面一直用的svn，所以对于git了解甚少。此次搭建个站接触到了git，借此发文总结。

## Git介绍
### Git和SVN
虽然SVN和Git都有使用到，但也仅仅是会日常的一些操作，对于它们背后的原理和流程不甚了解。
熟悉SVN的对Git嗤之以鼻，熟悉Git的对SVN不屑一顾。工具本无对错，只有熟悉和用在适合的地方而已。
总的来说，SVN是集中式的版本控制系统，而Git是分布式。分布式中，每个人手中都有一套完整的版本库。
**Git和其他版本控制系统还有个最大的不同就是它有暂存区的概念。**

### Git和GitHub
GitHub为Git提供仓库托管服务，是Git的远程仓库，要付费选择不公开仓库代码。
正常工作中，跟SVN一样，找一台电脑充当服务器的角色，每天24小时开机，
其他每个人都从这个“服务器”仓库克隆一份到自己的电脑上，
并且各自把各自的提交推送到服务器仓库里，也从服务器仓库中拉取别人的提交。


## 在本机创建Git仓库（repository）
个人理解是通过此步骤，讲本地项目与Git关联起来，便于Git命令行对项目操作以及跟踪文件操作的历史记录。
### 1、安装Git：（windows下）
[Git下载地址](https://git-for-windows.github.io)
直接next到底即可。随便打开文件目录，右键看到【Git Bash Here】则安装成功。
安装完成后，进行个人身份验证：(可填GitHub上的账户名和密码)
```
$ git config --global user.name "Your Name"    //--g 表示此台电脑所有用户可用
$ git config --global user.email "email@example.com"
```
### 2、选择适合的项目文件夹执行`$ git init`即可，在文件夹根目录会生成.git文件夹


## Git对项目文件的常用操作：
### 新增文件：
往Git版本库里添加的时候，是分两步执行的：
先提交至暂存区，在提交到分支。之后所有的操作也是如此。
- 执行`$ git status`可以掌握仓库文件当前的状态【此步骤可省】
- 执行`$ git add .`将文件修改添加到暂存区
- 执行`$ git commit -m "提交要备注的信息"`将暂存区内容提交到当前分支
- 执行`$ git push`将新增的内容上传到远程仓库中（GitHub或者服务器）

### 删除文件：
正常情况下：
- 1、直接在项目中删除
- 2、执行`$ git status`,提醒删除的文件（可省略）
- 3、执行`$ git rm`和`$ git commit -m "提交要备注的信息"`删除并提交
- 4、万一删错了执行 `$ git checkout -- 文件名.后缀名`
笔者通常是在项目中删除后，直接commit，目前未发现异常情况

### 版本回退：
实际开发过程中，会遇到各种情况需要回复到上一版本的情况，Git提供`git log`来查看历史记录。
git log命令显示从最近到最远的提交日志，输入`$ git log --pretty=oneline`可以只输出版本号和改变内容，更加精简。
```
$ git reset --hard HEAD^ // 回退到上一版本
$ git reset --hard HEAD^^  //回退到上上一版本
$ git reset --hard HEAD~5   //回退到往上5个版本
//若只想回退到往上第二版本，却到了第三版本，执行
$ git reset --hard 3628164（第二版本commit id，可在git log中查到）
```
### 管理修改：
由于Git存在**暂存区概念**，所以一定要先全部修改到暂存区，最后一次性执行commit，否则会出现有些文件修改后没有被提交。

### 创建合并分支：
让在同一个项目下的文件可以分布在不同的分支，互不干扰，完成后可合并。
- 创建dev分支，然后切换到dev分支：`$ git checkout -b dev`
- 查看当前分支`$ git branch` ，带星号
- 切换分支`$ git checkout 分支名`
- 将dev分支合并至主分支`$ git merge dev`
- 删除dev分支`$ git branch -d dev`
- 查看分支`$ git branch`


## Git与GitHub相互传输：
### GitHub → Git
- 在个人GitHub平台下新建仓库
- 在本机选择合适路径执行`$ git clone git@github.com:用户名/仓库名.git`

### Git → GitHub
- 第一步相同，新建仓库
- 按照空的新项目，GitHub给出的命令行依次执行
本地项目推送至GitHub就拥有分布式版本库。

## 更新远程代码到本地仓库
- 查看远程仓库`$ git remote -v`
- 从远程获取最新版本到本地`$ git fetch origin master`
- 比较本地的仓库和远程参考的区别`$ git log -p master.. origin/master`
- 把远程下载下来的代码合并到本地仓库，远程的和本地的合并`$ git merge origin/master`