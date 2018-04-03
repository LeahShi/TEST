---
title: redis简介及安装备份
date: 2018-01-10 19:48:32
tags: [Redis]
categories: work
---

Redis和Memcached、MongoDB类似，也是分布式缓存服务器，因为是缓存服务器，占据内存，所以比关系型数据库访问的速度快很多。同时对于高并发处理，redis远大于tomcat的承受能力，redis大概最大能承受50万的并发量，而tomcat只能达到几百，同时redis是单线程的，所以我们通常会将一些经常访问的资源放到redis中缓存，减轻服务器和数据库的压力。之后会持续记录redis相关操作...此篇主要记录redis安装步骤

<!-- more-->
 
如果只是学习redis的操作，可以下载window版本，直接解压，运行客户端和服务端即可；如果是部署到Linux上去，则需要编译，因为redis是有c++写的，linux上压缩包相当于是源码，而且linux的厂商也不同，所以需要使用c++编译。

1、安装gcc环境
```
yum install gcc-c++
```

如果你和我也报了一样的错误：

```
14] PYCURL ERROR 6 - "Couldn‘t resolve host ‘ftp.isu.edu.tw‘"Trying other mirror.
http://ftp.stu.edu.tw/Linux/CentOS/6.5/os/i386/Packages/mlocate-0.22.2-4.el6.i686.rpm: [Errno 14] PYCURL ERROR 6 - "Couldn‘t resolve host ‘ftp.stu.edu.tw‘"Trying other mirror.
```

就需要修改 /etc/resolv.conf 配置，在原有的nameserver之上多加一行 nameserver 8.8.8.8    

2、上传linux版本的redis,并解压
```
//使用CRT，alt+p进入上传窗口，执行 
put F:\java\redis\redis-3.2.8.tar.gz（本地redis压缩包）
// 切换回原来窗口
tar -zxvf redis-3.2.8.tar.gz -C /usr/local(目录随意)
```

3、编译并安装
```
// 进入redis解压的目录，执行
make
make install PREFIX=/usr/local/redis

```

4、启动
方法一： 切换到安装目录/usr/local/redis/bin，执行./redis-server -- 该种方法不太好是，当前窗口不能再有其他的操作，否则服务就停了
方法二： 复制源码包中的redis.conf文件到redis的安装目录下的bin目录  cp /usr/local/redis-3.0.0/redis.conf  /usr/local/redis/bin/
    并修改该配置文件中的“daemonize no”改成“daemonize yes”，在输入 ./redis-server ./redis.conf 启动

5、查看和关闭进程
```
ps aux|grep redis
kill 6379
```

6、启动客户端：
```
/usr/local/redis/bin/redis-cli -h ip地址 -p 6379
auth ""		引号内要填写密码
			
以上命令输入ip地址和密码前提是：修改redis.conf配置文件 

service iptables stop  关闭linux防火墙
```
			

