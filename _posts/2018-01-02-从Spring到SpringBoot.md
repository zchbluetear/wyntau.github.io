---
layout: post
title: "从Spring到Springboot"
date: 2018-01-02
description: "从Spring到Springboot"
tag: Spring
---

近期学习了慕课网电商平台的搭建，从Spring到SpringBoot。

特总结下学到的知识点。

1.LogBack

开源的日志记录框架。

核心模块：logback-core,logback-classic,logback-access.

logback-classic是log4J的改良版本。它实现了SLF4J API。

2.主从库同步与读写分离

主库负责写数据,然后会把对应的改变记录到binary log。 从库有一个开启的I／O 线程去监控这个文件的变化。如果有改动，就会把binary log写入到自己的Reply log。 然后由数据库本身的SQL Thread去读取Reply Log。做从库的更新操作。

读写分离：

代码的实现主要是基于拦截器对请求的方法做选择处理。

update,delete,insert -->主库   			select-->从库

3.DES加密

主要是对数据库的用户名和密码的加密。

它是一种对称的加密方式。即： 加密和解密使用相同的密钥。

使用Java自带的类可以实现。

4.缓存技术（减缓数据库压力）

客户端发起请求，先去缓存中读取。如果没有读取到值，则去数据库读取。返回数据库读取的数据，并写入缓存。

java使用jedis来操作Redis。


5.定时任务Quarz

（1）定义Job. 指定Job作用的类和方法。

（2）定义Trigger. 指定Job的执行频率。cron表达式实现。

（3）定义Schedule. 将Trigger和Job放到调度器去执行。











