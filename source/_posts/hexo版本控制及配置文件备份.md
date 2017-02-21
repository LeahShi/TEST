---
title: hexo配置文件备份总结
date: 2017-02-21 16:42:35
tags: [GitHub, Hexo]
categories: work
---

个站搭建起来后，在家里和公司都会有更新。但是换了电脑后，因为在原来电脑上面的修改如果没有备份云盘的话，很多东西都丢失了。
如果每次在修改后压缩备份在download的替换，很麻烦，而且容易忘记。
在网上找了很多资料[formulahendry童鞋](https://formulahendry.github.io/2016/12/04/hexo-ci/)的方案给了我思路，借以总结。

<!-- more -->

## 利用GitHub托管源码
执行`git push`把源文件托管GitHub(Source Repo)，所以流程变成了：
```
//step 1 ：将修改的文件部署至网站
$ hexo g -d
//step 2 : 将源码托管至GitHub
$ git status → $ git add . → $ git commit -m "modify" → $ git push
//step 3 : 换电脑后更新远程代码到本地仓库
$ git remote -v                     //查看远程仓库
$ git fetch origin master           //从远程获取最新版本到本地
$ git log -p master.. origin/master //比较本地的仓库和远程参考的区别`
$ git merge origin/master           //把远程下载下来的代码合并到本地仓库，远程的和本地的合并
```
上述过程依然麻烦....

## 持续集成，一键发布
对于前端工程化自动化，了解甚少，也是今后努力的方向，只是能理解，却不能讲述背后的流程。直接搬砖啦
### 持续集成（Continuous integration）：
当有新的change push到Source Repo时，自动构建生成最新的静态网站。
公司项目中我们架构师有用到的是Jenkins，本站根据教程使用的[AppVeyor](https://ci.appveyor.com/login)

### AppVeyor建立CI步骤
1、使用GitHub登录并添加源码项目
2、在项目根目录添加appveyor.yml文件【注意缩进不能省】
```
clone_depth: 5

environment:
  access_token:
    secure: [Your GitHub Access Token]

install:
  - node --version
  - npm --version
  - npm install
  - npm install hexo-cli -g

build_script:
  - hexo generate

artifacts:
  - path: public

on_success:
  - git config --global credential.helper store
  - ps: Add-Content "$env:USERPROFILE\.git-credentials" "https://$($env:access_token):x-oauth-basic@github.com`n"
  - git config --global user.email "%GIT_USER_EMAIL%"
  - git config --global user.name "%GIT_USER_NAME%"
  - git clone --depth 5 -q --branch=%TARGET_BRANCH% %STATIC_SITE_REPO% %TEMP%\static-site
  - cd %TEMP%\static-site
  - del * /f /q
  - for /d %%p IN (*) do rmdir "%%p" /s /q
  - SETLOCAL EnableDelayedExpansion & robocopy "%APPVEYOR_BUILD_FOLDER%\public" "%TEMP%\static-site" /e & IF !ERRORLEVEL! EQU 1 (exit 0) ELSE (IF !ERRORLEVEL! EQU 3 (exit 0) ELSE (exit 1))
  - git add -A
  - if "%APPVEYOR_REPO_BRANCH%"=="master" if not defined APPVEYOR_PULL_REQUEST_NUMBER (git diff --quiet --exit-code --cached || git commit -m "Update Static Site" && git push origin %TARGET_BRANCH% && appveyor AddMessage "Static Site Updated")

```
以上文件需要替换[Your GitHub Access Token]。
3、关于[生成Access Token](https://help.github.com/articles/creating-an-access-token-for-command-line-use/):
- 到github，点击个人头像找到【settings】
- 在侧边栏找到【Personal access tokens】
- 点击【Generate new token】，填写描述，勾选对应选项，点击【Generate token】拿到Access Token
![](/images/CI.jpg)
4、到[AppVeyor加密页面](https://ci.appveyor.com/tools/encrypt)把Access Token加密之后再替换[Your GitHub Access Token]
5、设置Appveyor。在源码的【settings】→【Environment】添加变量：
- STATIC_SITE_REPO就是github上放置发布后代码的地址（并非个站的https地址）
- TARGET_BRANCH就项目默认分支，一般默认为master
- GIT_USER_EMAIL和GIT_USER_NAME为GitHub账号的信息。
6、将源码托管GitHub，网站自动。

## 部署中遇到的坑
1、添加appveyor.yml文件时，没有缩进，执行错误
```
Error parsing appveyor.yml: "environment" section must be a mapping. (Line: 2, Column: 13)
```
2、执行成功后，一篇空白。。。
原因next主题没有提交。
解决方法：
将next作为[submodule](https://git-scm.com/book/zh/v1/Git-%E5%B7%A5%E5%85%B7-%E5%AD%90%E6%A8%A1%E5%9D%97)提交，将themes/next 打包备份后删除，
执行`git submodule add git@github.com:iissnan/hexo-theme-next themes/nextt`将next主题添加为源码的子模块，（不删除会报错该文件夹不为空），讲备份的文件夹中修改的文件替换过来即可。

