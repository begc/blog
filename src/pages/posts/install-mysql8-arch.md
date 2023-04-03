---
layout: '../../layouts/MarkdownPost.astro'
title: 'Centos 9 Stream 安装 mysql 8'
pubDate: 2023-04-03
description: '学习搭建mysql 8'
cover:
    url: 'https://p.ipic.vip/et8ed5.jpg'
    square: 'https://p.ipic.vip/et8ed5.jpg'
    alt: 'cover'
tags: ["hadoop","节点搭建"]
theme: 'light'
featured: false
---
# Centos 9 Stream 安装 Mysql8

## 1、Adding the MySQL Repository

```shell
wget https://repo.mysql.com//mysql80-community-release-el9-1.noarch.rpm
sudo dnf install mysql80-community-release-el9-1.noarch.rpm
```

![](https://p.ipic.vip/yj9lag.png) 

## 2、Installing MySQL 8.0

```xml
sudo dnf install mysql-community-server 
```

![](https://p.ipic.vip/uygwl6.png)

![](/Users/zhouruijie/Library/Application%20Support/marktext/images/2023-04-03-10-04-30-image.png)

## 3、开启 mysql 服务

```shell
sudo systemctl start mysqld 
```

## 4、Securing MySQL

```shell
grep 'A temporary password is generated' /var/log/mysqld.log | tail -1 
```

![](https://p.ipic.vip/vvazil.png)

查看临时密码，后续更改密码。

## 5、先用临时密码进入，修改密码等级，否则会无法修改密码。

```shell
mysql -uroot -p 
输入临时密码
alter user 'root'@'localhost' identified by '95uoh16j!@Abc';
更改后修改等级

SHOW VARIABLES LIKE 'validate_password%';
SET GLOBAL validate_password.length = 6;
SET GLOBAL validate_password.number_count = 0;
SET GLOBAL validate_password.policy=LOW;

alter user 'root'@'localhost' identified by '95uoh16j';
```

![](https://p.ipic.vip/h670q0.png)


