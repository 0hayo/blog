---
title: Git与Github进行SSH连接
author: 0hayo
avatar: https://cdn.jsdelivr.net/gh/honjun/cdn@1.6/img/custom/avatar.jpg
authorLink: 
authorAbout: nothing
authorDesc: nothing
categories: 
    - git
    - github
    - ssh
date: 2020-04-14 16:43:17
comments: true
tags: 
    - git
    - github
    - ssh
keywords: 0hayo
description: 站在自己所热爱的世界里！
photos: https://cdn.jsdelivr.net/gh/honjun/cdn@1.6/img/loader/orange.progress-bar-stripe-loader.svg
---

### 安装Git
1、到Git官网下载与你正在使用的操作系统(本文以windows为例)相对应的文件。一般地，选择64-bit Git for Windows Setup
2、安装时注意：勾选添加git到环境变量；在Windows Explorer Integration中勾选Git Bash Here。其余配置默认即可
3、安装完成后(可能需要注销或重启)，在任意一个文件夹空白处右键，检查是否有Git Bash Here的选项

### 设置邮箱和用户名

打开Git Bash，输入命令均在Git Bash中进行

```js
git config --global user.name "WangRongsheng"
git config --global user.email "603329354@qq.com"
```

### 设置邮箱和用户名

```js
ssh-keygen -t rsa -C "603329354@qq.com"
```

回车之后会出现如下所示的输出，直接按回车即可：

```js
Generating public/private rsa key pair.
Enter file in which to save the key
(/Users/your_user_directory/.ssh/id_rsa): (按回车键)
Enter passphrase (empty for no passphrase): (按回车键)
Enter same passphrase again: (按回车键)
```

### 将公钥添加到github中

1、在C:\user\xxx\.ssh\文件夹中找到id_rsa.pub这个文件，用文本编辑器(如记事本)打开，复制里面的所有内容
2、登陆github账号，点击头像旁的小三角展开，点击settings-SSH and GPG keys-New SSH key，在Title中取一个名字（任意），key中粘贴你刚刚复制的内容。然后点击Add SSH key即可

### 测试是否关联成功

输入：

```js
ssh -T git@github.com
```

出现以下结果即为成功：

```js
Hi WangRongsheng! You've successfully authenticated, but GitHub does not provide shell access.
```