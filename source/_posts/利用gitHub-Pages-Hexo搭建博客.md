---
title: GitHub Pages + Hexo搭建博客
date: 2016-02-15 2:02:22
tags: [GitHub, Hexo]
categories: work
comments: true
---

## 写在前面

这是一篇使用GitHub Pages和Hexo搭建个人博客姿势总结。

Hexo是个快速搭建博客的框架，它也有丰富的UI主题，几乎不用自己写任何界面。很是方便
而GitHub众所周知的免费代码托管仓库（同性交友平台），大神经常出没...

### 整个主要分为两大步：
- GitHub及Hexo的安装和配置
- 利用Hexo发布内容

<!-- more -->

[关于对个人博客的期许和历程戳这里](https://leahshi.github.io/2017/02/14/终于上线/)

### 对这俩词儿听说过么？观光团子们坐稳了~

- [Git](https://git-scm.com/book/zh/v2)
- [GitHub](https://github.com/)
- [GitHub Pages](https://pages.github.com/)
- [Hexo](https://hexo.io/zh-cn/)

- - -

## GitHub及Hexo的安装和配置

在安装前需要下载node和git
- [node.js](https://nodejs.org/en/)
- [git](https://git-scm.com/download/win)

### 配置GitHub
- 注册并登陆GitHub官网新建项目 【New repository】
![项目信息填写](/images/github_step1.jpg)

- 在项目的【setting】里找到【GitHub Pages】下的【Source】选择master branch → save
- 在浏览器中打开网址
```
https://GitHub用户名.github.io,至此GitHub配置网址完成
```

### 配置SSH（为了本地用git操作github上的文件）
#### 1、生成SSH
- 检查是否已经有SSH Key，打开Git Bash，输入
```
cd ~/.ssh
```
- 如果没有这个目录，则生成一个新的SSH，输入
```
ssh-keygen -t rsa -C "your e-mail"
```
#### 2、复制公钥内容到Github账户信息中
打开~/.ssh/id_rsa.pub文件，(在c盘user目录下)复制里面的内容；
打开Github官网，登陆后进入到个人设置(点击头像->setting)，点击右侧的SSH Keys，点击Add SSH key；填写title之后，将之前复制的内容粘贴到Key框中，最后点击Add key即可。
#### 3、测试SSH是否配置成功
在本地项目目录下输入
```
ssh -T git@github.com
```
若输出下面语句则配置成功
```
Hi username! You've successfully authenticated, but GitHub does not
provide shell access.
```
- - -

### 安装Hexo及本地建站
- 选择本地文件夹放置项目，用npm install -g hexo-cli 命令安装Hexo
- 初始化 hexo init
- 安装npm包 npm install
- 解决执行hexo d 报错 npm install hexo-deployer-git --save
- 测试是否成功 hexo s 显示hexo默认效果
- [hexo官方文档](https://hexo.io/zh-cn/docs/setup.html)

### 将网站内容发布至GitHub上
打开项目根目录下的_config.yml文件，找到如下位置，填写
```
# Deployment
## Docs: http://hexo.io/docs/deployment.html
deploy:
type: git
repo: git@github.com:Github账户名/Github账户名.github.io
```
yml文件中，:后面都是要带空格的

打开下面链接，可以看到刚才本地服务默认的hexo界面
```
http://Github账户名.github.io
```
- - -

## 利用Hexo发布内容
### 选择HexoUI主题（以本站为例_next主题）
1、下载对应主题：
```
git clone https://github.com/iissnan/hexo-theme-next themes/next
```
2、打开项目根目录下的_config.yml文件，找到theme字段，将其修改为对应的主题名称
```
# Extensions
## Plugins: http://hexo.io/plugins/
## Themes: http://hexo.io/themes/
theme: next
```
3、本地测试效果
```
hexo s -debug
```
4、上传至GitHub
```
hexo g -d
```
[更多Hexo主题](https://hexo.io/themes/)

### 发布内容
1、在网站存放的根目录打开git bash，输入：
```
hexo n "文章标题"
```
2、打开source/_post/文章标题.md，在- - -下方添加正文内容编辑；
使用markdown语法。[语法查阅递送门](http://wowubuntu.com/markdown/)
3、给文章贴标签加分类:
tages:[tag1, tag2, tag3, ...]
categories:XXX
4、保存执行一下命令即刻更新
```
hexo g -d
```
### 草稿【相当于私密内容】
1、新建草稿`hexo new draft "new draft"`
2、把草稿变成文章`hexo publish [layout] <filename>`
### [Hexo主题配置](https://hexo.io/zh-cn/docs/configuration.html)

### 绑定个性域名地址
太穷啦~~~~不做啦，略！



