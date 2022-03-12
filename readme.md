# 使用说明

1. 修改docker-compose.yaml中 `db`、`redis` 中的密码为你自己的。
2. 同步修改`config/conf.ini` 中`Database.Password`、`Redis.Password` 中对应的值
3. `docker-compose up -d`
4. 使用反向代理 例如 `nginx`进行配置。给上一个配置样例：
    ```nginx
     server {
        listen 8445 ssl http2;
        listen [::]:8445 ssl http2;
        server_name cloudreve.scjtqs.com;
        # 修改成你自己的证书
        ssl_certificate /usr/syno/etc/certificate/ReverseProxy/dbe894ad-e105-4248-af9f-5ad77b327d59/fullchain.pem;
        ssl_certificate_key /usr/syno/etc/certificate/ReverseProxy/dbe894ad-e105-4248-af9f-5ad77b327d59/privkey.pem;
        add_header Strict-Transport-Security "max-age=15768000; includeSubdomains; preload" always;
        location / {
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Host $http_host;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Host $server_name;
            proxy_set_header X-Forwarded-Proto https;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_pass http://127.0.0.1:5212;
            proxy_redirect off;
            # 如果您要使用本地存储策略，请将下一行注释符删除，并更改大小为理论最大文件尺寸
            # client_max_body_size 20000m;
        }
     }
    ```
5. aria配置：
   > 如果要开启ipv6 （`IPV6_MODE`）请使用 `network_mod: host`，并注释掉 
   > ```
   > # ports:
   > #  - 6803:6803
   > #  - 6888:6888/udp   
   > ```
   > 这段配置。并修改你的`RPC_SECRET`秘钥。
