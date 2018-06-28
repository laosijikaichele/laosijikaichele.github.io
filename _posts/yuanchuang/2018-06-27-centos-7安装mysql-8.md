---
layout: post
title: CentOS-7安装Mysql-8
category: 原创
tags: CentOS-7,Mysql-8
description: CentOS-7安装Mysql-8
---


[参考](https://www.digitalocean.com/community/tutorials/how-to-install-mysql-on-centos-7)

1.获取rpm
>wget [https://dev.mysql.com/get/mysql80-community-release-el7-1.noarch.rpm](https://dev.mysql.com/get/mysql80-community-release-el7-1.noarch.rpm "https://dev.mysql.com/get/mysql80-community-release-el7-1.noarch.rpm")

2.安装rpm
>sudo rpm -ivh mysql80-community-release-el7-1.noarch.rpm

3.安装mysql
>sudo yum install mysql-server

4.修改配置文件
>sudo vi /etc/my.cnf

5.添加下面1行
>[mysqld]
innodb_buffer_pool_size=64M ##新添加

6.启动mysql
>sudo systemctl start mysqld

7.查看运行状态
>sudo systemctl status mysqld

8.查看初始密码：
>sudo grep 'temporary password' /var/log/mysqld.log

9.修改初始密码(必须包含数字，大小写字母个一个特殊字符)：
>sudo mysql_secure_installation

10.登陆：
>mysql -u root -p
