# 背景
```
ufw 是 ubuntu 的一个自带的防火墙。 18.04 是自带安装的。
```

如果没有安装的话：
```
apt-get install ufw -y
```

# 命令
查看utf状态：
```
root@shell:~/.acme.sh/api.virtualsim.xyz# ufw status
Status: active

To                         Action      From
--                         ------      ----
22                         ALLOW       Anywhere
80                         ALLOW       Anywhere
443                        ALLOW       Anywhere
22 (v6)                    ALLOW       Anywhere (v6)
80 (v6)                    ALLOW       Anywhere (v6)
443 (v6)                   ALLOW       Anywhere (v6)
```

允许某个服务：
```
ufw allow ssh
ufw allow http
```

关闭某个服务：
```
ufw deny http
```

添加某个 IP 到端口:
```
# 允许某个 ip 访问我们的 8000 端口
ufw allow from 1.2.3.4 to any port 8000

# 允许某个 ip 访问我们的 8000 端口 以 TCP 协议
ufw allow from 1.2.3.4 to any port 8000 proto tcp
```

删除某个规则：
```
root@shell:~# ufw status
Status: active

To                         Action      From
--                         ------      ----
22                         ALLOW       Anywhere
22 (v6)                    ALLOW       Anywhere (v6)
80/tcp (v6)                DENY        Anywhere (v6)

root@shell:~# ufw delete 3
Deleting:
 deny 80/tcp
Proceed with operation (y|n)? y
Rule deleted (v6)
root@shell:~#
```

