---
title: Linux备忘录
# index_img: /img/acrodesign/linux.png
categories:
- Linux
---

## 前言
{% note success %}
    linux命令长时间不写就会忘记，所以写篇博客记录一下！
{% endnote %}

## SSH

使用 [Win32-OpenSSH](https://github.com/PowerShell/Win32-OpenSSH) 运行服务器，首先打开cmd，输入

```
ssh root@127.0.0.1
```
其中root为账户名，127.0.0.1为ip地址，当然您也可以使用在.ssh中设置的别名

.ssh文件夹位于 %USERPROFILE% 目录下 `C:\Users\Administrator.DESKTOP-1A2DGNK.ssh`

```
Host moc
	HostName 127.0.0.1
	User root
	ServerAliveInterval 60
	IdentityFile C:\Users\Administrator.DESKTOP-1A2DGNK\.ssh\moc
```
* moc 别名
* User 账户
* ServerAliveInterval 心跳间隔
* IdentityFile 密钥文件

经过上述配置后，您可以使用以下的命令来进行链接
```
ssh moc
```

>如果要尝试使用以上命令，则必须在环境变量中配置ssh否则无法直接使用！

## SSH安全性
{% note danger %}
如果仅设置了密码登录，存在被爆破的风险，而且root用户无法远程登录
{% endnote %}

这里采用Win32-OpenSSH为例，介绍如何使用密钥登录的方式来防范风险
* 首先打开`Win32-OpenSSH`的文件 , 点击`ssh-keygen.exe`运行

一共需要输入3次，分别为

* 密钥的文件名
* 密钥的密码
* 密码确认密码

> 若不想输入密码 2、3次直接按回车即可

输入完成后会在根目录下生成文件

* 文件名
* 文件名.pub

其中后缀名为.pub需要放入在服务器上，打开ssh远程链接
```
#进入.ssh目录
cd .ssh

#创建authorized_keys文件，如果存在则直接编辑即可
touch authorized_keys >> 您的pub文件内的内容
```

pub文件的内容是这种形式 ssh-rsa xxxxxx

**没有后缀名的文件为登录私钥，一定要妥善保存，否则后续可能会无法链接到服务器！！！！**

* 然后打开ssh配置项

```BASH
cd /etc/ssh
vi sshd_config
```

修改以下配置
* **允许密钥登录**
PubkeyAuthentication yes

* **允许root登录（此项可能存在风险）**
PermitRootLogin yes

* **存放pub密钥的文件路径**
AuthorizedKeysFile .ssh/authorized_keys .ssh/authorized_keys2

* **关闭密码登录（即仅允许密钥方式的登录）**
PasswordAuthentication no

以上配置保存完毕后，输入 systemctl restart sshd.service 重启ssh服务，即可完成上述配置！

## 包管理器
linux中的包管理器真的是非常的好用，仅需要几个字母便可以直接安装/卸载/更新软件

{% note success %}

<span style="color:green;"> \# 安装 </span>
apt install xxx

<span style="color:green;"> \# 重新安装</span>
apt reinstall xxx

<span style="color:green;"> \# 移除</span>
apt remove xxx

<span style="color:green;"> \# 包源更新</span>
apt update

<span style="color:green;"> \# 更新</span>
apt upgrade

{% endnote %}

由于国内特殊的网络原因，导致访问这些软件时下载更新很慢，鉴于此可以参考使用[清华大学镜像源](https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/)来解决此问题

使用vi编辑/etc/apt/sources.list,修改如下

{% note success %}
<span style="color:green;">\# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释</span>
**deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy main restricted universe multiverse**

\# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy main restricted universe multiverse
**deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-updates main restricted universe multiverse**

\# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-updates main restricted universe multiverse
**deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-backports main restricted universe multiverse**

\# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-backports main restricted universe multiverse

\# deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-security main restricted universe multiverse
\# # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-security main restricted universe multiverse

**deb http://security.ubuntu.com/ubuntu/ jammy-security main restricted universe multiverse**
\# # deb-src http://security.ubuntu.com/ubuntu/ jammy-security main restricted universe multiverse

\# 预发布软件源，不建议启用
\# deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-proposed main restricted universe multiverse
\# # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-proposed main restricted universe multiverse
{% endnote %}

使用包管理器安装的部分软件可能版本过老，如果遇到此情况的话请参考下列官方文档教程处理（其他软件大同小异不做过多赘述）

* [Nginx](https://nginx.org/en/linux_packages.html#Ubuntu)
* [Redis](https://redis.io/docs/getting-started/installation/install-redis-on-linux/)

## 系统代理

打开/etc/environment文件

在文件中增加如下内容，然后:wq保存后reboot机器即可正常使用
```BASH
http_proxy="http://ip:port/"
https_proxy="http://ip:port/"
no_proxy="localhost, 127.0.0.1"
```

## 开机自启
打开 `/etc/systemd/system/` 文件夹

新建一个文件，文件名称以`xxx.service`格式

然后键入如下的内容
```BASH
[Unit]
Description=subconverter

[Service]
WorkingDirectory=/root/subconverter
ExecStart=/root/subconverter/subconverter
Restart=always
RestartSec=3 
SyslogIdentifier=dotnet-example
User=root

[Install]
WantedBy=multi-user.target
```
* Description 说明
* WorkingDirectory 工作目录
* ExecStart 执行命令
* RestartSec 启动次数
* User 运行的角色

编辑完成后输入如下的命令
* systemctl daemon-reload 重载
* systemctl enable xxx.service 启用服务
* systemctl restart xxx.service 启动服务
* systemctl status xxx.service 获取服务状态

## 参考
[1] [Linux备忘录](https://blog.dreamogi.com/posts/linux-memo/)
