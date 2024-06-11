<h1 align="center">ChatGPT Mirror</h1>

[![License](https://img.shields.io/github/license/dairoot/ChatGPT-Mirror)](https://github.com/dairoot/ChatGPT-Mirror/blob/main/LICENSE)
[![Docker](https://img.shields.io/docker/v/dairoot/chatgpt-mirror?label=ChatGPT-Mirror)](https://hub.docker.com/r/dairoot/chatgpt-mirror)

ChatGPT Mirror 后台是一个 ChatGPT 镜像网站，允许多账号共享管理。实现多人同时使用 ChatGPT 服务。

## 特点

- 提供与官网同等的极致体验。
- 用户无需翻墙，便可轻松访问并使用 ChatGPT 官方网站的所有功能。
- 通过在 `Mirror` 后台录入 `ChatGPT Token`，让团队成员每人拥有独立账号 (或共享同一个`ChatGPT Plus`账号)。
- 提供便捷的管理后台，帮助管理员高效管理账号。

## 使用方法

<!-- [![IMAGE ALT TEXT HERE](https://member.bilibili.com/ed6f9a02-54e5-41a4-bc3f-7ad7b9aeb282)](https://www.bilibili.com/video/BV1fD421M7xP/) -->

<a href="https://www.bilibili.com/video/BV1fD421M7xP/" target="_blank">
  <img src="./docs/cover.jpeg"  alt="使用方法">
</a>

## 在线体验

```
https://chatgpt.dairoot.cn/
账号：dairoot
密码：dairoot
```

## 本地运行

```bash
# 本地需要翻墙
vi worker/wrangler.toml # 修改管理后台账号密码

docker compose up

caddy run --config ./Caddyfile --watch

访问: https://localhost/
```

## 部署到服务器（海外 vps）

1. 运行

```bash
vi worker/wrangler.toml # 修改管理后台账号密码

docker compose pull # 更新镜像

docker compose up -d # 后台运行
```

2. 配置 nginx （需要配置 https）

```bash
upstream chatgpt {
    server 127.0.0.1:8787;
    server 127.0.0.1:8788;
    server 127.0.0.1:8789;
}

server {
    listen              443 ssl http2;
    listen              [::]:443 ssl http2;
    server_name         chatgpt.example.com;

    # SSL 文件
    ssl_certificate     /etc/nginx/ssl/chatgpt.example.com/fullchain.crt;
    ssl_certificate_key /etc/nginx/ssl/chatgpt.example.com/private.pem;


    # 日志文件
    # access_log /data/logs/ngx.chatgpt.access.log json_combined;
    access_log /data/logs/ngx.chatgpt.access.log;
    error_log /data/logs/ngx.chatgpt.error.log;


    # 静态文件
    location /fe/ {
       alias /home/chatgpt-mirror/admin/dist/;
    }

    location / {
        proxy_redirect off;
        proxy_set_header Host $host;
        proxy_pass http://chatgpt;
    }


}

# HTTP redirect
server {
    listen      80;
    listen      [::]:80;
    server_name chatgpt.example.com;
    return      301 https://chatgpt.example.com$request_uri;
}
```
