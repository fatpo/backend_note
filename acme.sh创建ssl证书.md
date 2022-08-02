中文教程：
```
https://www.cnblogs.com/clsn/p/10040334.html#12-acmesh
```
英文教程：
```
https://github.com/acmesh-official/acme.sh
```
写在前面1：
```
如果要搞 ssl 证书，前提是 DNS 已经配置好了映射。
```
写在前面2：
```
在用 acme 命令前，nginx 不要提前配置 443 端口！！
在用 acme 命令前，nginx 不要提前配置 443 端口！！
在用 acme 命令前，nginx 不要提前配置 443 端口！！
```


第一次申请证书，让我们弄个账户：
```
root@shell:~/.acme.sh#  acme.sh --issue  -d api.abc.xyz  --nginx
[Sun Dec  5 13:53:44 UTC 2021] Using CA: https://acme.zerossl.com/v2/DX12130
[Sun Dec  5 13:53:45 UTC 2021] No EAB credentials found for ZeroSSL, let's get one
[Sun Dec  5 13:53:45 UTC 2021] acme.sh is using ZeroSSL as default CA now.
[Sun Dec  5 13:53:45 UTC 2021] Please update your account with an email address first.
[Sun Dec  5 13:53:45 UTC 2021] acme.sh --register-account -m my@example.com
[Sun Dec  5 13:53:45 UTC 2021] See: https://github.com/acmesh-official/acme.sh/wiki/ZeroSSL.com-CA
[Sun Dec  5 13:53:45 UTC 2021] Please add '--debug' or '--log' to check more details.
[Sun Dec  5 13:53:45 UTC 2021] See: https://github.com/acmesh-official/acme.sh/wiki/How-to-debug-acme.sh
root@shell:~/.acme.sh# acme.sh --register-account -m 123@qq.com
[Sun Dec  5 13:54:38 UTC 2021] No EAB credentials found for ZeroSSL, let's get one
[Sun Dec  5 13:54:39 UTC 2021] Registering account: https://acme.zerossl.com/v2/DV9110
[Sun Dec  5 13:54:40 UTC 2021] Registered
[Sun Dec  5 13:54:40 UTC 2021] ACCOUNT_THUMBPRINT='FAKEADAGqDrC4-cVtNVS12312312EIxB_K12Nee2m75k74Ys598'
```

第二次申请证书：
```
root@shell:~/.acme.sh# acme.sh --issue  -d api.abc.xyz  --nginx
[Sun Dec  5 13:55:08 UTC 2021] Using CA: https://acme.zerossl.com/v2/DX12130
[Sun Dec  5 13:55:08 UTC 2021] Single domain='api.abc.xyz'
[Sun Dec  5 13:55:08 UTC 2021] Getting domain auth token for each domain
[Sun Dec  5 13:55:11 UTC 2021] Getting webroot for domain='api.abc.xyz'
[Sun Dec  5 13:55:11 UTC 2021] Verifying: api.abc.xyz
[Sun Dec  5 13:55:11 UTC 2021] Nginx mode for domain:api.abc.xyz
[Sun Dec  5 13:55:11 UTC 2021] Found conf file: /etc/nginx/conf.d/abc.conf
[Sun Dec  5 13:55:11 UTC 2021] Backup /etc/nginx/conf.d/abc.conf to /root/.acme.sh/api.abc.xyz/backup/api.abc.xyz.nginx.conf
[Sun Dec  5 13:55:11 UTC 2021] Check the nginx conf before setting up.
[Sun Dec  5 13:55:11 UTC 2021] OK, Set up nginx config file
[Sun Dec  5 13:55:11 UTC 2021] nginx conf is done, let's check it again.
[Sun Dec  5 13:55:11 UTC 2021] Reload nginx
[Sun Dec  5 13:55:15 UTC 2021] Processing, The CA is processing your order, please just wait. (1/30)
[Sun Dec  5 13:55:18 UTC 2021] Success
[Sun Dec  5 13:55:18 UTC 2021] Restoring from /root/.acme.sh/api.abc.xyz/backup/api.abc.xyz.nginx.conf to /etc/nginx/conf.d/abc.conf
[Sun Dec  5 13:55:18 UTC 2021] Reload nginx
[Sun Dec  5 13:55:18 UTC 2021] Verify finished, start to sign.
[Sun Dec  5 13:55:18 UTC 2021] Lets finalize the order.
[Sun Dec  5 13:55:18 UTC 2021] Le_OrderFinalize='https://acme.zerossl.com/v2/DX12130/order/gts9RFHAw1NVcKq-pPuvWQ/finalize'
[Sun Dec  5 13:55:19 UTC 2021] Order status is processing, lets sleep and retry.
[Sun Dec  5 13:55:19 UTC 2021] Retry after: 15
[Sun Dec  5 13:55:35 UTC 2021] Polling order status: https://acme.zerossl.com/v2/DX12130/order/gts9RFHAw1NVcKq-pPuvWQ
[Sun Dec  5 13:55:36 UTC 2021] Downloading cert.
[Sun Dec  5 13:55:36 UTC 2021] Le_LinkCert='https://acme.zerossl.com/v2/DX12130/cert/WMqtmDy-VVk8BDzXCySFjw'
[Sun Dec  5 13:55:37 UTC 2021] Cert success.
-----BEGIN CERTIFICATE-----
MIIGcjCCBFqgAwIBAgIQXKNAHmgaZMUUSS4BvexcpDANBgkqhkiG9w0BAQwFADBL
MQswCQYDVQQGEwJBVDEQMA4GA1UEChMHWmVyb1NTTDEqMCgGA1UEAxMhWmVyb1NT
TCBSU0EgRG9tYWluIFNlY3VyZSBTaXRlIENBMB4XDTIxMTIwNTAwMDAwMFoXDTIy
MDMwNTIzNTk1OVowHTEbMBkGA1UEAxMSYXBpLnZpcnR1YWxzaW0ueHl6MIIBIjAN
BgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAw8LM35PqIxqm2UIXtBTtG+3Jb03O
WtFFjlUpnXGKjydhZs69ALXqN4acpSdYzDhC8TrofpvL9Th2GzYLeJktfnOFfSwm
hfGtbTzsk4Qd+d7L1AO4+wDyt6r6ZRZR/g/u6fLxWUouChJEu3q/CHQnGTZ3kolH
uqb+f6z/psrUElUJcXolQFJjlGE9xx4NlOqZswrdOY/M2ssr0Do7OOxKh8Ui1sX3
ojKAoqhTk6EH3IPg+59fZ7RdOG/IPkoALiozP/b0U/SA6vpzPab13iyymp0YMzMw
X1WaOt5CU2UsTMeEY6ou7hsf9iZ4HA==
-----END CERTIFICATE-----
[Sun Dec  5 13:55:37 UTC 2021] Your cert is in: /root/.acme.sh/api.abc.xyz/api.abc.xyz.cer
[Sun Dec  5 13:55:37 UTC 2021] Your cert key is in: /root/.acme.sh/api.abc.xyz/api.abc.xyz.key
[Sun Dec  5 13:55:37 UTC 2021] The intermediate CA cert is in: /root/.acme.sh/api.abc.xyz/ca.cer
[Sun Dec  5 13:55:37 UTC 2021] And the full chain certs is there: /root/.acme.sh/api.abc.xyz/fullchain.cer
```
检查下：
```
root@shell:~/.acme.sh#
root@shell:~/.acme.sh# "/root/.acme.sh"/acme.sh --cron --home "/root/.acme.sh"
[Sun Dec  5 13:59:57 UTC 2021] ===Starting cron===
[Sun Dec  5 13:59:57 UTC 2021] Already uptodate!
[Sun Dec  5 13:59:57 UTC 2021] Upgrade success!
[Sun Dec  5 13:59:57 UTC 2021] Auto upgraded to: 3.0.2
[Sun Dec  5 13:59:57 UTC 2021] Renew: 'api.abc.xyz'
[Sun Dec  5 13:59:57 UTC 2021] Skip, Next renewal time is: Thu Feb  3 13:55:37 UTC 2022
[Sun Dec  5 13:59:57 UTC 2021] Add '--force' to force to renew.
[Sun Dec  5 13:59:57 UTC 2021] Skipped api.abc.xyz
[Sun Dec  5 13:59:57 UTC 2021] Renew: 'abc.xyz'
[Sun Dec  5 13:59:57 UTC 2021] Skip invalid cert for: abc.xyz
[Sun Dec  5 13:59:57 UTC 2021] Skipped abc.xyz
[Sun Dec  5 13:59:57 UTC 2021] ===End cron===
```

然后nginx配置好路径：
```
  server {
        listen 443;
        listen [::]:443;

        server_name api.abc.xyz;
        ssl on;
        root html;
        index index.html index.htm;
        #ssl_certificate /etc/nginx/ssl/api.abc.xyz/fullchain.cer;
        #ssl_certificate_key /etc/nginx/ssl/api.abc.xyz/api.abc.xyz.key;
        ssl_certificate   /root/.acme.sh/api.abc.xyz/api.abc.xyz.cer;
        ssl_certificate_key  /root/.acme.sh/api.abc.xyz/api.abc.xyz.key;
        ssl_session_timeout 5m;
        ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
        ssl_prefer_server_ciphers on;

        client_max_body_size 20m;

        location ~* \.(html|gif|jpg|jpeg|png|css|js|ico)$ {
            root /root/www/Abc_BackEnd/static/;
        }
        location /static/ {
            root /root/www/Abc_BackEnd/;
        }

        location ~* ^/(user|config|report)/  {
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

如果想换证书位置：
```
acme.sh  --installcert  -d  api.abc.xyz   \
        --key-file   /etc/nginx/ssl/api.abc.xyz/api.abc.xyz.key \
        --fullchain-file /etc/nginx/ssl/api.abc.xyz/fullchain.cer \
        --reloadcmd  "service nginx force-reload"
```
