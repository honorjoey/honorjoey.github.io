---
layout:     post   				    # 使用的布局（不需要改）
title:      配置ubuntu允许root用户远程登录				# 标题 
subtitle:   通过修改配置文件，实现ubuntu的root用户远程登录 #副标题
date:       2020-03-12 				# 时间
author:     HonorJoey 						# 作者
header-img: img/post-bg-algorithm.jpg 	#这篇文章标题背景图片
catalog: true 						# 是否归档
tags:								#标签
    - ubuntu
    - linux
---
### 安装ssh工具
#### 获取root权限
```shell script
sudo su
```
#### 安装openssh-server
 ```shell script
sudo apt install openssh-server
```
#### 编辑sshd_config文件
```shell script
vi /etc/ssh/sshd_config
```
将
```.env
#PermitRootLogin prohibit-password
```
改成
```..env
PermitRootLogin yes
```
保存退出
#### 修改root密码
```shell script
passwd
```
然后两次输入要设置的登录密码
#### 远程登录
输入
```shell script
ifconfig
```
查看IP地址后即可在远程通过
```shell script
ssh -p 22 root@x.x.x.x
```
登录ubuntu
#### 修改端口号
22为默认端口号，在编辑sshd_config文件时可以修改默认的登录端口号
找到端口号
```..env
#Port 22
```
删除前面的#，修改后面的22为想要修改的端口号，保存退出即可。

