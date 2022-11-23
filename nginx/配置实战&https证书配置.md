## 链接
[CSDN: 使用acme.sh给Nginx配置HTTPS证书](https://blog.csdn.net/aouoy/article/details/115748036)

## 0. nginx配置项目
新增配置文件 `cat /etc/nginx/conf.d/haha.conf`：
```
upstream myapp{
    server 127.0.0.1:8000 weight=1;
}

server {
    listen      80;
    listen [::]:80;

    listen 443 ssl;

    server_name api.haha.xyz;

    client_max_body_size 20m;

    location ~* \.(html|gif|jpg|jpeg|png|css|js|ico)$ {
        root /root/www/haha/static/;
    }
    location /static/ {
        root /root/www/haha/;
    }

    location ~* ^/haha_api/ {
        proxy_set_header        REMOTE_ADDR     $proxy_add_x_forwarded_for;

        add_header Access-Control-Allow-Origin *;
        add_header Access-Control-Allow-Methods 'GET, POST, OPTIONS';
        add_header Access-Control-Allow-Headers 'DNT, X-Mx-ReqToken, Keep-Alive, User-Agent, X-Requested-With, If-Modified-Since, Cache-Control, Content-Type, Authorization';

        if ($request_method = 'OPTIONS') {
            return 204;
        }

        proxy_set_header   Host             $host:8000;
        proxy_set_header   X-Real-IP        $remote_addr;
        proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
        proxy_set_header   Cookies          $http_cookies;
        proxy_pass http://myapp;
    }
}
```
然后重启：
```
nginx -t
nginx -s reload
```
此时此刻，应该可以`curl http://api.haha.xyz`



## 1. 安装 acme.sh
```
curl  https://get.acme.sh | sh
source ~/.bashrc
```
设置为自动更新
```
acme.sh  --upgrade  --auto-upgrade
```

## 2. 生成证书

安装命令
```
acme.sh --issue -d 域名 --nginx /usr/local/nginx/conf/conf.d/域名配置
```
比如：
```
acme.sh --issue -d api.haha.xyz --nginx /etc/nginx/conf.d/haha.conf
```

它安装完毕后，会输出：
```
-----END CERTIFICATE-----
[Wed Nov 23 07:33:58 UTC 2022] Your cert is in: /root/.acme.sh/api.haha.xyz/api.haha.xyz.cer
[Wed Nov 23 07:33:58 UTC 2022] Your cert key is in: /root/.acme.sh/api.haha.xyz/api.haha.xyz.key
[Wed Nov 23 07:33:58 UTC 2022] The intermediate CA cert is in: /root/.acme.sh/api.haha.xyz/ca.cer
[Wed Nov 23 07:33:58 UTC 2022] And the full chain certs is there: /root/.acme.sh/api.haha.xyz/fullchain.cer
[Wed Nov 23 07:33:58 UTC 2022] Installing key to: /root/.acme.sh/api.haha.xyz/api.haha.xyz.key
[Wed Nov 23 07:33:58 UTC 2022] Installing full chain to: /root/.acme.sh/api.haha.xyz/fullchain.cer
[Wed Nov 23 07:33:58 UTC 2022] Run reload cmd: nginx -s reload
[Wed Nov 23 07:33:58 UTC 2022] Reload success
```


## 3. copy/安装 证书
安装命令
```
acme.sh --install-cert -d 域名 \
--key-file       域名证书key  \
--fullchain-file 域名证书cert \
--reloadcmd     "nginx -s reload"
```
例如：
```
acme.sh --install-cert -d api.haha.xyz 
--key-file /root/.acme.sh/api.haha.xyz/api.haha.xyz.key 
--fullchain-file  /root/.acme.sh/api.haha.xyz/fullchain.cer 
--reloadcmd     "nginx -s reload"
```


## 4. Nginx添加https配置
修改/etc/nginx/conf.d/haha.conf， 添加这一段：
```
    ssl_certificate   /root/.acme.sh/api.haha.xyz/fullchain.cer;
    ssl_certificate_key  /root/.acme.sh/api.haha.xyz/api.haha.xyz.key;
    ssl_session_timeout 5m;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;
```
总的配置：
```
upstream myapp{
    server 127.0.0.1:8000 weight=1;
}

server {
    listen      80;
    listen [::]:80;

    listen 443 ssl;

    server_name api.haha.xyz;

    ssl_certificate   /root/.acme.sh/api.haha.xyz/fullchain.cer;
    ssl_certificate_key  /root/.acme.sh/api.haha.xyz/api.haha.xyz.key;
    ssl_session_timeout 5m;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;

    client_max_body_size 20m;

    location ~* \.(html|gif|jpg|jpeg|png|css|js|ico)$ {
        root /root/www/haha/static/;
    }
    location /static/ {
        root /root/www/haha/;
    }

    location ~* ^/haha_api/ {
        proxy_set_header        REMOTE_ADDR     $proxy_add_x_forwarded_for;

        add_header Access-Control-Allow-Origin *;
        add_header Access-Control-Allow-Methods 'GET, POST, OPTIONS';
        add_header Access-Control-Allow-Headers 'DNT, X-Mx-ReqToken, Keep-Alive, User-Agent, X-Requested-With, If-Modified-Since, Cache-Control, Content-Type, Authorization';

        if ($request_method = 'OPTIONS') {
            return 204;
        }

        proxy_set_header   Host             $host:8000;
        proxy_set_header   X-Real-IP        $remote_addr;
        proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
        proxy_set_header   Cookies          $http_cookies;
        proxy_pass http://myapp;
    }
}
```
ps:
```
这里的key-file路径对应到Nginx配置的ssl_certificate_key路径，fullchain-file路径对应到Nginx配置的ssl_certificate路径。
```
重启Nginx使https配置生效
```
nginx -t
nginx -s reload
```

## 5. 撤销和强制更新证书
强制更新证书 `acme.sh --renew -f -d 域名`，如：
```
acme.sh --renew -f -d api.haha.xyz
```

撤销证书 `acme.sh --renew -f -d 域名`，如：
```
acme.sh --revoke -d api.haha.xyz
```
