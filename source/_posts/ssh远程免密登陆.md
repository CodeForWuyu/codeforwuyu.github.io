---
title: ssh远程免密登陆
image: /images/ssh.png
date: 2021-05-11 21:05:55
tags: [linux]
categories: 
- linux
---

>  被登陆服务器为A机器
>
>  客户端机器为B机器

1. B机器执行命令生成公钥和私钥

   ~~~cmd
   ssh-keygen -t rsa
   ~~~

   生成的秘钥在用户目录下，例如C:\Users\admin\ .ssh

2. 复制公钥文件内容：id_rsa.pub

3. A机器同样执行生成ssg-keygen的命令

   > 在用户目录.ssh目录下创建authorized_keys文件，将第二步复制的内容粘贴到该文件中

4. 修改A机器上的权限

   - chmod 700 .ssh
   - chmod 600 authorized_keys
   - 修改StrictModes(/etc/ssh/sshd_config文件中)项必须设置成no；

5. 测试连接
   在B机器上命令行输入测试

   ~~~cmd
   ssh 用户名@A机器ip地址
   ~~~

   如果没有提示输入密码，直接登录成功，则表示免密登录配置成功！





