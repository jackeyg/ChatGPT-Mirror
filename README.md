# ChatGPT Mirror

`ChatGPT Mirror 后台` 是一个 ChatGPT 镜像网站，允许多账号共享管理。实现多人同时使用 ChatGPT 服务。提供与官网一模一样的体验感！

- 用户无需翻墙，即可轻松访问并体验 ChatGPT 官方网站的全部功能。
- 通过批量申请 ChatGPT 账号并录入 `ChatGPT Mirror 后台`，实现团队内人均拥有独立账号。
- 提供管理后台，方便管理员对账号进行高效管理。

## 使用方法

视频

## 本地运行

```bash
# 本地需要翻墙
cp worker/wrangler.example.toml worker/wrangler.toml # 修改管理后台账号密码

docker compose up

caddy run --config ./Caddyfile --watch

访问: https://localhost/
```

## 部署到服务器（海外 vps）

1. 运行

```bash
cp worker/wrangler.example.toml worker/wrangler.toml # 修改管理后台账号密码

docker compose pull # 更新镜像

docker compose up -d # 后台运行
```

2. 配置 nginx （需要配置 https）

```bash
upstream chatgpt {
    server 127.0.0.1:8787;
    server 127.0.0.1:8788;
    server 127.0.0.1:8789;
    server 127.0.0.1:8790;
    server 127.0.0.1:8791;
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
