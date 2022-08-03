# 背景
加固服务器, fail2ban 可以把那些重试我们 SSH 的坏用户拉到黑名单。

`man fail2ban` :
```
fail2ban - a set of server and client programs to limit brute force authentication attempts.

一组服务器和客户端程序，用于限制暴力验证尝试。
```

# 安装并开启
```
sudo apt-get update
sudo apt-get upgrade

sudo apt-get install -y fail2ban

sudo systemctl start fail2ban
sudo systemctl enable fail2ban
```

# 配置
`vi /etc/fail2ban/jail.local`
```
[sshd]
enabled = true
port = 22
filter = sshd
logpath = /var/log/auth.log
maxretry = 3
```

重启下：
```
sudo systemctl restart fail2ban
```

# 效果
一天的量：
```
root@shell:~# cat /var/log/auth.log | wc -l
1563
```
给你们看看部分坏用户：
```
Aug  3 03:47:23 wahaha sshd[15609]: Invalid user oracle from 51.77.116.67 port 58878
Aug  3 03:47:24 wahaha sshd[15609]: Connection closed by invalid user oracle 51.77.116.67 port 58878 [preauth]
Aug  3 03:51:48 wahaha sshd[15635]: Invalid user visuelconcept from 79.104.0.82 port 35548
Aug  3 03:51:48 wahaha sshd[15635]: Disconnected from invalid user visuelconcept 79.104.0.82 port 35548 [preauth]
Aug  3 03:51:58 wahaha sshd[15637]: Invalid user ailina from 148.63.215.173 port 55568
Aug  3 03:51:58 wahaha sshd[15637]: Disconnected from invalid user ailina 148.63.215.173 port 55568 [preauth]
Aug  3 03:52:29 wahaha sshd[15640]: Invalid user oracle from 103.160.24.2 port 49862
Aug  3 03:52:30 wahaha sshd[15640]: Disconnected from invalid user oracle 103.160.24.2 port 49862 [preauth]
Aug  3 03:52:56 wahaha sshd[15642]: Invalid user ailina from 134.17.16.43 port 15371
Aug  3 03:52:57 wahaha sshd[15642]: Disconnected from invalid user ailina 134.17.16.43 port 15371 [preauth]
Aug  3 03:53:20 wahaha sshd[15644]: Invalid user joseph from 201.236.182.196 port 37452
Aug  3 03:53:20 wahaha sshd[15644]: Disconnected from invalid user joseph 201.236.182.196 port 37452 [preauth]
Aug  3 03:53:43 wahaha sshd[15646]: Invalid user will from 165.227.142.62 port 53370
Aug  3 03:53:43 wahaha sshd[15646]: Disconnected from invalid user will 165.227.142.62 port 53370 [preauth]
Aug  3 03:54:41 wahaha sshd[15649]: Invalid user user from 177.92.98.254 port 53788
Aug  3 03:54:41 wahaha sshd[15649]: Disconnected from invalid user user 177.92.98.254 port 53788 [preauth]
Aug  3 03:54:45 wahaha sshd[15651]: Invalid user steve from 39.109.127.242 port 35102
Aug  3 03:54:45 wahaha sshd[15651]: Disconnected from invalid user steve 39.109.127.242 port 35102 [preauth]
Aug  3 03:55:30 wahaha sshd[15653]: Invalid user gerry from 52.12.139.92 port 46340
Aug  3 03:55:30 wahaha sshd[15653]: Disconnected from invalid user gerry 52.12.139.92 port 46340 [preauth]
Aug  3 03:57:09 wahaha sshd[15657]: Invalid user zr from 193.123.117.41 port 35666
Aug  3 03:57:09 wahaha sshd[15657]: Disconnected from invalid user zr 193.123.117.41 port 35666 [preauth]
Aug  3 03:59:33 wahaha sshd[15662]: Invalid user teampspeak3 from 189.135.237.156 port 54170
Aug  3 03:59:33 wahaha sshd[15662]: Disconnected from invalid user teampspeak3 189.135.237.156 port 54170 [preauth]
```


# 参考教程：
[https://www.techrepublic.com/article/how-to-install-fail2ban-on-ubuntu-server-18-04/](https://www.techrepublic.com/article/how-to-install-fail2ban-on-ubuntu-server-18-04/)
