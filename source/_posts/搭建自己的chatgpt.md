---
title: 搭建自己的chatgpt
index_img: /img/acrodesign/docker.png
categories:
- Docker
tags:
- Chatgpt
- NoSight
hide: true
---

## 前言
{% note success %}

最近 ChatGPT的热度大家是有目共睹的了，但是每次使用都要翻墙就有点烦了，怎么样能在不翻墙的情况下正常使用呢。那就不得不提到我下面说的这个项目了

**[ChatGPT-Next-Web](https://github.com/Yidadaa/ChatGPT-Next-Web)**  一键免费部署你的跨平台私人 ChatGPT 应用。 话不多说，看我表演

{% endnote %}

## 部署需要的东西
* **ChatGpt API Key**

* **liunx 代理端口**

>不知道自己的API key的可以进入 [API keys](https://platform.openai.com/account/api-keys) 页面查看
>
>如果需要部署该项目，你需要有一个可以正常使用的 apikey ，若没有额度则自行使用虚拟卡进行充值，虚拟卡可在淘宝购买
>
>liunx 代理端口 就是你在你的服务器上可以科学上网 `curl https://www.google.com` 可以连接 
>如果不知道怎么搭建的话可以看我这篇文章 {% post_link Linux部署Clash %}

## 开始

首先这个项目的安装方式有很多种，推荐大家用Docker进行部署，方便

1. 安装Docker，首先更新 apt 程序包索引，并安装程序包以允许 apt 通过 HTTPS 使用存储库。
若服务器已安装了Docker 请直接看 第5步
```liunx
 sudo apt update

 sudo apt install \
     ca-certificates \
     curl \
     gnupg \
     lsb-release
```

2. 接着添加 Docker 的官方 GPG 密钥：
```liunx
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

```

3. 使用以下命令设置 Docker 使用稳定版本 (stable) 的库：
```
echo \
 "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
 $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

4. 安装 Docker 引擎
```liunx
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

5. 安装 chatgpt-next-web
```liunx
docker pull yidadaa/chatgpt-next-web
```

6. 部署项目并设置代理
```
docker run -d -p 3000:3000 \
   -e OPENAI_API_KEY="sk-xxxx" \
   -e CODE="页面访问密码" \
   --net=host \
   -e PROXY_URL="http://127.0.0.1:7890" \
   yidadaa/chatgpt-next-web
```

7. 将域名反代到3000端口
```
location / {
    # 设置反向代理
    proxy_pass http://127.0.0.0:3000;
}
```

这样项目就部署成功，访问 网址：3000 即可体验自己的chatgpt
