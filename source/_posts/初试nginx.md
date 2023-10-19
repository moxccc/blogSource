---
title: 初试nginx
# index_img: /img/acrodesign/nginx.png
categories:
- Nginx
---

## 前言
{% note success %}
作为一年的前端程序员，到现在都没有一台自己的服务器，感觉有点拉跨，所以最近忍痛割爱，耗费巨资买了一台阿里云的一年服务器,虽然买了，但是不知道能用服务器干嘛，我想这不能让他闲着的原则，我就搞起了Linux和nginx在上面测试

Linux不用我多说，程序员必备技能， nginx呢是目前最火的服务器不用我多说，所以在这些因素下我就开始了痛苦的学习之旅
{% endnote %}

### 1.准备工作
{% note success %}
> 我购买的阿里云服务器配置： 2核(vCPU)2 GiB  Ubuntu 22.04 64位

* 安装使用工具 ：
Xshell 7 +  Xftp 7     {% label info @[Xshell官网](https://www.netsarang.com/en/xshell/) %}

* 连接你的账户 ：
在xshell下载安装完成后，打开xshell 登录你的账号，用户名一般默认root 密码则是你在阿里云服务器控制台重置的密码

{% endnote %}



### 2.开始使用
{% note info %}

*  **检查是否有更新**
``` linux
    apt update
```

*  **更新安装包**
``` linux
    apt upgrade
```

*  **安装nginx**
``` linux
    apt install nginx
```

*  **nginx常用命令**
``` nginx
    nginx -s stop        //立即停止服务

    nginx -s quit        //优雅退出(等待进程全部结束)

    nginx -s reload      //热重载

    ps aux | grep nginx  //查看nginx进程

```
*  **nginx常用目录**
``` nginx
    /etc/nginx/nginx.conf      //配置文件目录

    /usr/share/nginx/html      //网页文件目录

```
{% endnote %}

### 3.应用最新的安装包
{% note info %}
*  **nginx配置key**

``` linux
    wget https://nginx.org/keys/nginx_signing.key
```

``` linux
    sudo apt-key add nginx_signing.key
```

``` linux
    sudo sh -c "echo 'deb http://nginx.org/packages/mainline/ubuntu/ `lsb_release -cs` nginx' > /etc/apt/sources.list.d/Nginx.list"
```

*  **nginx更新**
``` nginx
    sudo apt-get update        //全部升级

    sudo apt-get install nginx        //升级nginx

```
{% endnote %}

### 4.踩坑专区
{% note warning %}
*  **修改 `/usr/share/nginx/html` 目录下的 `index.html` 不生效**

    在 `/etc/nginx/conf.d`  目录下新建一个 {% label info @你需要访问的网址%}.conf  // 例如 ： www.moxccc.com  => moxccc.conf 并进行如下配置
    ``` nginx
    server {
        listen       80;
        listen       443 ssl;
        server_name  moxccc.com www.moxccc.com frp.moxccc.com; //这里写你需要访问网址的url 有多个则用空格隔开

        charset utf-8;

        #如果是80端口则301转向到https
        if ($server_port = 80 ) {
            return 301 https://$host$request_uri;
        }

        #access_log  logs/host.access.log  main;
        location / {
            root   html;
            index  index.html index.htm;
        }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
    ```


{% endnote %}

## 参考
[1] [Linux下使用Let's Encrypt配置Nginx SSL证书](https://blog.dreamogi.com/posts/lets-encrypt-linux/)










