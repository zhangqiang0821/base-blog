---
title: redis笔记
date: 2017-01-13 09:21:54
tags: [redis,nosql]
categories: 数据库
---

redis常用操作

### windows下载

[下载地址](https://github.com/MSOpenTech/redis/releases)

### 启动命令
> redis-server redis.windows.conf

### 设置Redis服务
> redis-server --service-install redis.windows-service.conf --loglevel verbose

输入命令之后没有报错，表示成功了，刷新服务，会看到多了一个redis服务

### 常用的redis服务命令。

>卸载服务：redis-server --service-uninstall

>开启服务：redis-server --service-start

>停止服务：redis-server --service-stop