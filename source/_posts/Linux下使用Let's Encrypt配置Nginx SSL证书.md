---
title: Linux下使用Let's Encrypt配置Nginx SSL证书
index_img: /img/acrodesign/linux.png
categories:
- Linux
tags:
- SSL
- Nginx
---

## 前言
{% note success %}

白嫖的ssl证书谁能不爱呢，还可以设置自动续期，基本上算是永久的，话不多说，看我表演。

{% endnote %}

## 使用acme.sh
[acme.sh文档地址](https://github.com/acmesh-official/acme.sh)
### 开始安装

```ebnf
curl https://get.acme.sh | sh -s email=my@moxccc.com
```

### 安装完成进入`acme.sh`目录

```
cd ./.acme.sh
```

### 自动配置dns

由于我这里使用的是阿里云 来配置域名解析修改，其他的域名提供商请查看此处[dnsapi](https://github.com/acmesh-official/acme.sh/wiki/dnsapi#11-use-aliyun-domain-api-to-automatically-issue-cert)
* 首先去阿里云网站申请`AccessKey`( 请注意保管! )
* 然后修改acme的配置项
```BASH
cd /root/.acme.sh
```

* 修改account.conf
```
export Ali_Key="***"
export Ali_Secret="***"
```


### 颁发证书
```LINUX
acme.sh  --issue -d moxccc.com  -d '*.moxccc.com'  --dns dns_ali
```
> 若想颁发多个域名 则按照 `-d 域名.com`  `-d '*.域名.com'` 在 --dns 前添加即可


## 查看证书信息

```LINUX
acme.sh --info -d dreamogi.com
```

## 自动续期

由于证书的有效期为3个月,所以需要进行自动续期，具体内容参见[How to renew the certs](https://github.com/acmesh-official/acme.sh#12-how-to-renew-the-certs)

```LINUX
acme.sh --renew -d dreamogi.com --force
```

## 生成证书后重载Nginx

```LINUX
acme.sh --install-cert -d dreamogi.com \
--key-file       /path/to/keyfile/in/nginx/key.pem  \
--fullchain-file /path/to/fullchain/nginx/cert.pem \
--reloadcmd     "service nginx force-reload"
```

## Nginx 配置SSH

```nginx
# 证书配置
ssl_certificate      /root/.acme.sh/moxccc.com_ecc/fullchain.cer;
ssl_certificate_key  /root/.acme.sh/moxccc.com_ecc/moxccc.com.key;
```


