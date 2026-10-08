---
title: mysql8.0的安装
date: 2023-05-01
categories: ["database"]
---


本文主要介绍如何采用zip安装包的形式在win10系统上安装mysql8.0。

## 下载

在正式安装之前，需要从[官网](https://dev.mysql.com/downloads/mysql/)下载zip格式的mysql安装包。

## 安装

1. 设置环境变量
2. 打开以管理员身份运行cmd
3. 运行命令 `mysqld --initialize-insecure`
4. 安装mysql服务 `mysqld install`
5. 运行mysql服务 `net start mysql`

此时mysql已经安装完成，登录的密码可以在 `mysql根目录\data\` 的err格式文件中查看。
