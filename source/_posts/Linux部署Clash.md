---
title: Linux部署Clash
index_img: /img/acrodesign/clash.png
categories:
- Linux
tags:
- Clash
- NoSight
hide: true
---

## 前言
{% note info %}
想部署一个自己的chatgpt网站，不用科学魔法就可以访问的那种，当时写着写着遇到了问题，在linux部署好了但是老是响应延迟，后面查阅了相关资料发现，只有你linux服务器可以科学上网才能访问chatgpt的接口。好吧，有需求就有动力嘛，就自己动手，借助别人的教程在服务器上成功搭建Clash。
{% endnote %}

## 安装 Clash
1. 下载当前操作系统与 CPU 架构对应的包文件 [下载地址](https://github.com/Dreamacro/clash)
```
wget -O clash.gz https://github.com/Dreamacro/clash/releases/download/v1.6.5/clash-linux-amd64-v1.6.5.gz
```

2. 下载好后解压安装包中 clash 到 `/usr/local/bin/` 目录下，并删除压缩包文件
```ebnf
gzip -dc clash.gz > /usr/local/bin/clash
chmod +x /usr/local/bin/clash
rm -f clash.gz
```

3. 创建配置文件目录，并下载 MMDB 文件
```ebnf
mkdir /etc/clash
wget -O /etc/clash/Country.mmdb https://github.com/Dreamacro/maxmind-geoip/releases/download/20220812/Country.mmdb
```

4. 创建 systemd 脚本，脚本文件路径为 /etc/systemd/system/clash.service，内容如下：
```linux
[Unit]
Description=clash daemon
[Service]
Type=simple
User=root
ExecStart=/usr/local/bin/clash -d /etc/clash/
Restart=on-failure
[Install]
WantedBy=multi-user.target
```

5. 重载 systemctl daemon
```
systemctl daemon-reload
```

## 配置代理上网

1. 导入已有的科学上网 `订阅链接`

```
wget -O /etc/clash/config.yaml [你的订阅链接]
```

2. 设置系统代理，添加配置文件 /etc/environment 并在其中写入如下内容：
```
export http_proxy="127.0.0.1:7890"
export https_proxy="127.0.0.1:7890"
export no_proxy="localhost, 127.0.0.1"
```

3. 重启
```
reboot
```

4. 启动 clash 服务，并设置为开机自动启
```ebnf
systemctl start clash
systemctl enable clash
```

5. 测试 goolge.com 访问
```
# curl google.com
<HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
<TITLE>301 Moved</TITLE></HEAD><BODY>
<H1>301 Moved</H1>
The document has moved
<A HREF="http://www.google.com/">here</A>.
</BODY></HTML>
```

至此，Linux 下 clash 配置完成，可以愉快的上外网拉包了

