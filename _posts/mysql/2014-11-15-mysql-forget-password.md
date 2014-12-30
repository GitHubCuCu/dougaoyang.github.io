---
layout: post
title: mysql的root用户密码丢失解决办法
category: mysql数据库
description: 当root用户密码忘记时，通过跳过授权表的方法来登录mysql，并且修改密码
keywords: mysql, password, 授权
---

##1. 问题
当我们使用mysql数据库时，可能会遇到忘记mysql密码的情况，当遇到这种情况时，本人的第一反应是：坑爹啊，重装一个吧！（PS：感觉好弱吧），后来遇到的多了，也就有了解决的办法了。

##2. 解决办法
停止mysql进程
>service mysqld stop 或者 pkill mysqld

启动mysql，并且附加一个选项
>mysqld_safe --skip-grant-tables --user=mysql &

这样就可以在登录时跳过授权表mysql.user和mysql.db,登录mysql
>mysql -uroot

![登录mysql][1]

登录之后，就可以修改root的登录密码

    update mysql.user set password=password("123") where user="root" and host="localhost"; 

修改完密码后，要记得重启服务，就可以使用新密码正常登录了

####PS：修改用户密码还有下面几种方法
    set password for root@localhost=password("123");  
    set password=password("123"); 

[1]: /images/20131128225656062.png "文件大小"
